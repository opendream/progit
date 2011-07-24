# การแตก branch ใน Git #

เกือบทุก VCS support การแตก branch ทางใดซักทางหนึ่ง การแตก Branch หมายถึงคุณแยกตัวออกมาจาก main line ของการพัฒนาและทำงานต่อไปบนบนนั้นโดยไม่ไปยุ่งเกี่ยวกับ main line ในหลายๆ VCS การทำแบบนี้ค่อนข้างจะเปลือง ส่วนใหญ่จะเป็นการ copy ทั้ง directory ของ source code ซึ่งจะกินเวลานานมว๊ากกบน project ใหญ่ๆ

หลายคนเรียกการแตก branch ใน Git เป็น “killer feature” และมันทำให้ Git โดดเด่นออกมาจาก VCS อื่นๆ ทำไมน่ะเหรอ? เพราะวิธีที่ Git แตก branch มันถูกโคตร การแตก branch ทำได้ในชั่วพริบตาและการ switch ไปๆมาๆระหว่าง branch ก็เร็วพอๆกัน ไม่เหมือน VCS ดาษๆทั่วไป Git ผลักดันกระบวนการทำงาน workflow ให้แตก branch และ merge บ่อยๆแบบที่ทำได้วันละหลายๆครั้ง การทำความเข้าใจและบรรลุ feature นี้จะทำให้ Git กลายเป็นเครื่องมือที่ทรงพลังและมีเอกลักษณ์และทำให้วิถีการทำงานของคุณเปลี่ยนไปเลย

## Branch คืออะไร ##

เพื่อทำความเข้าใจวิธีที่ Git แตก branch เราต้องย้อนกลับมาดูว่า Git เก็บข้อมูลยังไง ตามที่คุณอาจจะจำได้จาก Chapter 1 ว่า Git ไม่ได้เก็บข้อมูลเป็นลำดับของความเปลี่ยนแปลงต่อเวลาแต่เก็บเป็นลำดับของ snapshot ต่อเวลา

เวลาคุณ commit ใน Git นั้น Git จะเก็บเป็น object ของการ commit ซึ่งประกอบด้วย pointer ชี้ไปยัง snapshot ของ content ที่คุณ stage ไว้, metadata ของชื่อผู้แก้ไขและ message ที่บันทึกไว้ และ pointer ที่ชี้ไปยัง parent ลำดับถัดไปของ commit นั้นๆ (ซึ่งอาจจะไม่มีก็ได้ถ้าเป็น commit ครั้งแรก, อาจจะมีอันเดียวชี้ไปยัง parent ของ commit ปรกติทั่วไปหรืออาจจะมี parent หลายอันสำหรับ commit ที่เป็นผลจากการ merge หลายๆ branch เข้าด้วยกัน)

ลองจินตนาการว่าคุณมี directory อันนึงที่มี file อยู่ข้างใน 3 files แล้วคุณก็ stage ทั้งหมดและ commit การ stage file จะสร้าง checksum ของแต่ละ file (ไอ้ SHA-1 hash ที่บอกไว้ใน Chapter 1 นั่นแหละ), แล้วบันทึก version ของ file นั้นๆใน Git repository (Git อ้างอิงพวกมันแบบ blobs) และเพิ่ม checksum นั้นลงไปใน staging area:

	$ git add README test.rb LICENSE
	$ git commit -m 'initial commit of my project'

เมื่อคุณทำการ commit ด้วยคำสั่ง `git commit` Git จะคำนวน checksum ของแต่ละ subdirectory (ในกรณีนี้ก็มีแค่ root project directory) และบันทึกโครงสร้างของ directory ใน Git repository หลังจากนั้น Git ก็จะสร้าง commit object ที่มี metadata และ pointer ชี้ไปยังโครงสร้างของ root project เพื่อที่มันจะได้สร้าง snapshot นั้นขึ้นมาใหม่ได้เมื่อต้องการ

Git repository ของคุณตอนนี้จะมี 5 objects: blob แต่ละ blob สำหรับ content ของแต่ละ file ใน 3 files นั้น, โครงสร้าง root directory ที่เก็บ list ของสิ่งของในนั้นและบันทึกว่า file ไหนถูกเก็บใส่ blob ไหน และ 1 commit ที่มี pointer อันนึงชี้ไปยังโครงสร้างของ root directory กับพวก metadata ของ commit นั้น ซึ่งหน้าตาของข้อมูลใน Git repository ของคุณก็มีคอนเซปประมาณรูป Figure 3-1.

Insert 18333fig0301.png 
Figure 3-1. Single commit repository data.

ถ้าคุณทำการแก้ไขใดๆ แล้ว commit ซ้ำอีกครั้ง commit อันถัดไปจะเก็บในรูป pointer ชี้ไปยัง commit ก่อนหน้า ทำไปอีก 2 commits history ของคุณน่าจะมีหน้าตาประมาณรูป Figure 3-2.

Insert 18333fig0302.png 
Figure 3-2. Git object data for multiple commits.

branch อันนึงใน Git เป็นแค่ pointer ฉบับกระเป๋าของ commits เหล่านี้ ชื่อโดย default ของ branch ใน Git คือ master ขณะที่คุณ commit ครั้งแรกส่งกำลังส่ง master branch อันนึงที่ points กลับไปยัง commit ก่อนหน้า ทุกครั้งที่คุณ commit มันก็ค่อยๆขยับไปๆโดยอัตโนมัติ

Insert 18333fig0303.png 
Figure 3-3. Branch pointing into the commit data’s history.

แล้วจะเกิดอะไรขึ้นถ้าคุณสร้าง branch ใหม่? ก็แค่สร้าง pointer อันใหม่เพื่อที่จะโยกย้ายไปมาตามใจ ยกตัวอย่างว่าคุณสร้าง branch ใหม่ชื่อว่า testing ซึ่งสามารถทำได้ด้วยคำสั่ง `git branch`:

	$ git branch testing

มันจะสร้าง pointer อันใหม่ใน commit ปัจจุบันที่คุณอยู่ (ดูรูป Figure 3-4).

Insert 18333fig0304.png 
Figure 3-4. Multiple branches pointing into the commit’s data history.

แล้ว Git มันรู้ได้ไงว่าตอนนี้คุณอยู่ branch ไหน? เพราะมันแอบจำ pointer พิเศษที่ชื่อว่า HEAD จำไว้ว่า HEAD นี้ต่างกันกับ concept ของ HEAD ใน VCS อื่นๆที่คุณอาจจะเคยใช้มาเยอะมาก (ไม่ว่าจะเป็น Subversion หรือ CVS) ใน Git นี่คือ pointer ชี้ไปยัง local branch ที่คุณทำงานอยู่ อย่างในกรณีนี้ คุณยังอยู่บน master คำสั่ง git branch แค่สร้าง branch ใหม่ให้เฉยๆ มันเปล่า switch คุณไปยัง branch ใหม่นั้นแต่อย่างใด (ดูรูป Figure 3-5).

Insert 18333fig0305.png 
Figure 3-5. HEAD file pointing to the branch you’re on.

ถ้าจะ switch ไปยัง branch ใดๆที่มีอยู่ คุณก็แค่สั่ง `git checkout` ลอง switch ไปยัง testing branch อันใหม่กัน:

	$ git checkout testing

คำสั่งนี้จะย้าย HEAD ให้ไปชี้ที่ testing branch (ดังรูป Figure 3-6).

Insert 18333fig0306.png
Figure 3-6. HEAD points to another branch when you switch branches.

แล้วมันสำคัญยังไงอ่ะ? อ่ะ มาดู commit ถัดมากัน:

	$ vim test.rb
	$ git commit -a -m 'made a change'

รูป Figure 3-7 จะโชว์ผลให้ดู

Insert 18333fig0307.png 
Figure 3-7. The branch that HEAD points to moves forward with each commit.

น่าสนใจนะเนี่ย เพราะตอนนี้ testing branch ของคุณขยับไปข้างหน้า แต่ master branch ยังคงชี้ไปยัง commit ที่คุณอยู่ก่อนหน้านี้ตอนที่ switch branch ด้วยคำสั่ง `git checkout` อ่ะ ลอง switch กลับไป master branch กัน:

	$ git checkout master

มาดูผลในรูป Figure 3-8

Insert 18333fig0308.png 
Figure 3-8. HEAD moves to another branch on a checkout.

คำสั่งนั้นทำสองอย่าง มันขยับ HEAD pointer กลับไปยัง master branch แต่มัน revert file ทั้งหลายใน working directory ของคุณกลับไปยัง snapshot ที่ master ชี้อยู่ ซึ่งหมายความว่าความเปลี่ยนแปลงทั้งหลายที่คุณแก้ไปตั้งแต่จุดนี้ถูกแยกออกไปจาก version เก่าของ project สรุปคือมัน rewind งานที่คุณทำไปบน testing branch กลับมาชั่วคราวเพื่อที่คุณจะได้ลองแก้ไปทางอื่นได้

มาลองแก้ file ซักนิดแล้ว commit อีกทีดู:

	$ vim test.rb
	$ git commit -a -m 'made other changes'

ตอนนี้ project history ของคุณถูกแยกออก (ดู Figure 3-9) คุณสร้าง branch ใหม่และ switch ไป, ทำงานไปบนนั้น, แล้ว switch กลับมาที่ branch หลัก แล้วทำงานอื่นลงไป ความเปลี่ยนแปลงทั้งสองสายถูกตัดขาดจากกันใน branch ทั้งสอง คุณสามารถโดดกลับไปกลับมาระหว่างสอง branches ได้แล้วค่อย merge มันเข้าด้วยกันเมื่อคุณพร้อม ซึ่งทั้งหมดนั่นทำได้ด้วยคำสั่งง่ายอย่าง `branch` และ `checkout`

Insert 18333fig0309.png 
Figure 3-9. The branch histories have diverged.

เนื่องจาก branch ใน Git จริงๆแล้วเป็นแค่ file ธรรมดาๆที่เก็บตัวหนังสือ 40 อักษรที่เป็น SHA-1 checksum ของ commit ที่มันชี้ไปหา การสร้างหรือทำลาย branch เลยถูกอย่างกะขี้ สร้าง branch ใหม่ทั้งง่ายและเร็วส์ปานการเขียน 41 bytes ลงไปใน file (40 อักษรกะ newline อีกตัว)

มันเลยแตกต่างกับการแตก branch ใน VCS tool ทั่วไปราวฟ้ากะเหว เพราะปรกติต้องนั่ง copy ทุก file ใน project ใส่ใน directory ใหม่ซึ่งกินเวลาหลายวิ หรืออาจจะเป็นนาทีขึ้นอยู่กับความอ้วนของ project ขณะที่ Git ตดไม่ทันหายเหม็นก็ทำเสร็จละ นอกจากนี้ เนื่องจากเราจำ parent ไว้ในทุกๆ commit เวลาต้องหาต้นตอ version เวลาจะ merge ก็ทำได้โดยอัตโนมัติและง่ายด้วย features เหล่านี้เติมความกล้าให้ developer สร้างและใช้ branches ทั้งหลายเยอะขึ้น

มาดูกันว่าทำไมคุณก็ควรจะทำ

## เบสิคการแตก Branch และการ Merge ##

มาดูตัวอย่างง่ายๆของการแตก branch และการ merge ด้วย workflow ที่คุณน่าจะใช้ในชีวิตประจำวัน คุณจะทำตามนี้:

1.	ทำงานบน web site.
2.	แตก branch สำหรับ story ใหม่ที่คุณกำลังทำ
3.	ทำงานใน branch นั้น

จังหวะนี้เอง มีโทรศัพท์เข้ามาบอกว่ามี issue ร้อนที่ต้องรีบ hotfix ด่วน! คุณก็ทำตามนี้:

1.	กลับไปยัง production branch
2.	แตก branch สำหรับทำ hotfix.
3.	หลังจาก test แล้ว ก็ merge hotfix branch แล้ว push ขึ้นไปยัง production
4.	switch กลับไป story ตอนแรก แล้วทำงานต่ออย่างสบายอารมณ์

### เบสิคการแตก Branch ###

ก่อนอื่น สมมติว่าคุณกำลังทำงานบน project คุณ และ commit เข้าไปหลายทีละ (ตาม Figure 3-10).

Insert 18333fig0310.png 
Figure 3-10. A short and simple commit history.

คุณตัดสินใจว่า เอาล่ะ วันนี้ทำ issue #53 ดีกว่า (ไม่ว่าระบบ issue-tracking ที่บริษัทคุณใช้จะเป็นอะไร) เอาจริงๆแล้ว Git ไม่ได้ผูกติดกับ issue-tracking แต่อย่างใด แต่เนื่องจาก issue #53 มันเป็นเรื่องเป็นราวของมันคุณก็เลยอยากแตก branch แยกออกไปทำ การสร้าง branch ใหม่และ switch ไปในจังหวะเดียวสามารถทำได้ด้วยคำสั่ง `git checkout` แล้วเสริม `-b` เข้าไป:

	$ git checkout -b iss53
	Switched to a new branch "iss53"

นี่คือท่าลัดของ:

	$ git branch iss53
	$ git checkout iss53

รูป 3-11 จะโชว์ผลให้ดู

Insert 18333fig0311.png 
Figure 3-11. Creating a new branch pointer.

คุณทำงานไปบน web site ของคุณและ commit ไปนิดหน่อย ซึ่งระหว่างนั้นก็เป็นการผลัก branch `iss53` ไปข้างหน้า เพราะคุณ checkedout มันออกมา (แปลว่า HEAD ของคุณชี้ไปหามัน ดังรูป Figure 3-12):

	$ vim index.html
	$ git commit -a -m 'added a new footer [issue 53]'

Insert 18333fig0312.png 
Figure 3-12. The iss53 branch has moved forward with your work.

แล้วโทรศัพท์ก็มาบอกว่า web site มีงานเข้า และคุณต้องซ่อมมันด่วน เพราะ Git คุณไม่จำเป็นต้อง deploy fix ของคุณไปกับความเปลี่ยนแปลงใน `iss53`  และคุณก็ไม่ต้องเปลืองแรงแก้ code กลับมาเป็นเหมือนเดิม ก่อนที่จะเริ่ม fix อะไรที่อยู่บน production ทั้งหมดที่ต้องทำก็แค่ switch กลับไปยัง master branch

อย่างไรก็ตาม ก่อนทำแบบนั้น จำไว้ว่า working directory หรือ staging area ของคุณมีความเปลี่ยนแปลงที่ยังไม่โดน commit ซึ่ง conflict กับ branch ที่คุณกำลัง checkout Git ก็เลยไม่ปล่อยให้คุณ switch branches ถ้าจะให้ดี คุณควรจะมีสถานะการทำงาน cleanๆ ก่อนที่จะ switch branches จริงๆมันก็มีท่ายากที่เอาไว้แก้สถานการณ์นี้เหมือนกันนะ (คือการ stash เข้าไปก่อนแล้วค่อย commit amending ตาม) แต่ค่อยมาว่ากันมีหลัง สำหรับตอนนี้ แค่ commit ความเปลี่ยนแปลงทั้งหมดเข้าไปก่อนละกัน จะได้ switch กลับไป master branch ได้:

	$ git checkout master
	Switched to branch "master"

ณ จุดนี้ working directory ของ project คุณจะเหมือนกับก่อนหน้าที่คุณเริ่มทำงานบน issue #53 เป๊ะๆ และคุณก็สามารถรวมสมาธิไปที่ hotfix ได้ ตรงนี้คือตรงที่ต้องจำให้ขึ้นใจ: Git จะ reset working directory ของคุณให้เหมือนกับ snapshot ของ commit ที่ branch ที่คุณ check out ชี้ไป มันจะเพิ่ม ลบ หรือแก้ file โดยอัตโนมัติจนมั่นใจว่า working copy ของคุณเหมือนกับ commit สุดท้ายบน branch นั้น

หลังจากนั้น คุณมี hotfix ที่รอให้ทำ มาสร้าง branch เพื่อทำ hotfix จนกว่ามันจะเสร็จกัน(ดู Figure 3-13):

	$ git checkout -b 'hotfix'
	Switched to a new branch "hotfix"
	$ vim index.html
	$ git commit -a -m 'fixed the broken email address'
	[hotfix]: created 3a0874c: "fixed the broken email address"
	 1 files changed, 0 insertions(+), 1 deletions(-)

Insert 18333fig0313.png 
Figure 3-13. hotfix branch based back at your master branch point.

คุณสามารถ run tests เพื่อให้มั่นใจว่า hotfix มันทำงานได้ดั่งใจและ merge มันกลับเข้า master branch เพื่อ deploy ใส่ production ซึ่งสามารถทำได้ด้วยคำสั่ง `git merge`:

	$ git checkout master
	$ git merge hotfix
	Updating f42c576..3a0874c
	Fast forward
	 README |    1 -
	 1 files changed, 0 insertions(+), 1 deletions(-)

คุณจะเห็นคำว่า "Fast forward" ใน merge นั้น เพราะ commit ที่ถูกชี้โดย branch ที่คุณ merge มันเป็น upstream ของ commit ที่คุณอยู่โดยตรง Git ก็เลยขยับ pointer ไปข้างหน้า พูดอีกนัยหนึ่งก็คือ เวลาที่คุณพยายามจะ merge commit ซักอันเข้ากับ commit ที่สามารถไปถึงได้โดยการตาม history ของ commit อันแรก Git จะทำให้ทุกอย่างง่ายขึ้นโดยการขยับ pointer ไปข้างหน้าเพราะมันไม่มีงานที่ถูกแยกออกไปให้ merge สิ่งนี้เรียกว่า "fast forward".

ความเปลี่ยนแปลงของคุณตอนนี้อยู่ใน snapshot ของ commit ที่ `master` branch ชี้ไป และคุณก็สามารถ deploy ความเปลี่ยนแปลงนี้ได้ (ดัง Figure 3-14).

Insert 18333fig0314.png 
Figure 3-14. Your master branch points to the same place as your hotfix branch after the merge.

หลังจาก fix ที่โคตรสำคัญถูก deployed คุณก็พร้อมที่จะ switch กลับไปยังงานที่คุณทำค้างไว้ก่อนถูกขัดจังหวะ อย่างไรก็ตาม สิ่งที่คุณจะทำก่อนคือการ delete branch `hotfix` เพราะว่าคุณไม่ต้องใช้มันอีกแล้ว เพราะไอ้ `master` branch มันชี้ไปที่จุดเดียวกันแล้ว คุณสามารถ delete มันด้วย option `-d` ของ `git branch`:

	$ git branch -d hotfix
	Deleted branch hotfix (3a0874c).

เอาล่ะ ตอนนี้ switch กลับไป branch issue #53 ที่ทำค้างไว้ได้ละ แล้วก็ทำงานต่อตามสบาย (ดังรูป Figure 3-15):

	$ git checkout iss53
	Switched to branch "iss53"
	$ vim index.html
	$ git commit -a -m 'finished the new footer [issue 53]'
	[iss53]: created ad82d7a: "finished the new footer [issue 53]"
	 1 files changed, 1 insertions(+), 0 deletions(-)

Insert 18333fig0315.png 
Figure 3-15. Your iss53 branch can move forward independently.

ถึงงานที่ทำไว้ใน `hotfix` branch จะไม่อยู่ใน files ใน `iss53` branch ก็ช่างหัวมัน ถ้าต้องดึงมันเข้ามา คุณก็สามารถ merge `master` branch ของคุณใส่ใน `iss53` branch ได้ด้วยคำสั่ง `git merge master` หรือว่าจะรอ integrate ความเปลี่ยนแปลงพวกนั้นตอนจะรวม `iss53` branch กลับเข้า `master` ทีหลังก็ได้

### เบสิคการ Merge ###

สมมติว่าคุณมั่นแล้วว่า issue #53 นี่เนียนแล้วและพร้อมที่จะ merge มันเข้า `master` branch คุณก็ merge branch `iss53` (เหมือนกะตอนที่ทำ branch `hotfix` ก่อนหน้าอ่ะแหละ) ที่ต้องทำทั้งหมดก็แค่ check out branch ที่อยากจะ merge เข้าไปใส่ และสั่ง command `git merge`:

	$ git checkout master
	$ git merge iss53
	Merge made by recursive.
	 README |    1 +
	 1 files changed, 1 insertions(+), 0 deletions(-)

อันนี้อาจจะดูต่างกะตอน merge `hotfix` ก่อนหน้านิดส์นึง เพราะครั้งนี้ history การเปลี่ยนแปลงมันถูกแยกออกไปจากจุดก่อนหน้า นั่นเพราะว่า commit ของ branch ที่คุณกะลังอยู่ไม่ได้เป็นรากเหง้าโดยตรงของ branch ที่กำลังจะ merge เข้ามา Git เลยต้องออกแรง อย่างในกรณีนี้ Git ก็ทำ การ merge 3 ทางง่ายๆ โดยใช้ 2 snapshots ที่อยู่ที่ปลายทั้ง 2 ข้างของแต่ละ branch และรากเหง้าของทั้งสองที่เหมือนกัน Figure 3-16 จะ highlight 3 snapshots ที่ Git ใช้ในการ merge กรณีนี้ให้ดู

Insert 18333fig0316.png 
Figure 3-16. Git automatically identifies the best common-ancestor merge base for branch merging.

แทนที่จะแค่ขยับ pointer ของ branch ไปข้างหน้า Git สร้าง snapshot อันที่ที่เป็นผลจากการ merge 3 ทางนี้ และสร้าง commit อันใหม่ที่ชี้ไปยัง snapshot นั้นโดยอัตโนมัติ (see Figure 3-17) ปรกติเราเรียกท่านี้ว่า merge commit และความพิเศษของมันคือ มันมีแม่มากกว่า 1 อัน

อย่างนึงที่อยากจะอวดคือ Git ไปคุ้ยหารากเหง้าที่ซ้ำกันของทั้งสองกิ่งให้เพื่อที่จะใช้เป็น merge base ซึ่ง CVS อื่นหรือ Subversion (ก่อน version 1.5) ไม่มีปัญญา และ developer ที่จะ merge ต้องไปคุ้ยเองว่า merge base ที่ดีที่สุดคืออันไหน ด้วยเหตุนี้การ merge ใน Git ก็เลยง่ายกว่าระบบอื่นๆมว้ากกกส์

Insert 18333fig0317.png 
Figure 3-17. Git automatically creates a new commit object that contains the merged work.

เอาล่ะ หลังจากงานคุณถูก merge เข้าไปเรียบร้อยแล้ว คุณก็ไม่จำเป็นต้องเลี้ยง branch `iss53` ให้เสียข้าวสุก ลบแม่มเลย แล้วก็ไปปิด ticket ในระบบ ticket-tracking

	$ git branch -d iss53

### เบสิคของ Merge Conflicts ###

ในบางเวลาที่ไม่เป็นใจ ถ้าคุณแก้ส่วนเดียวกันใน file เดียวกันไปคนละทิศคนละทางบน 2 branch ที่แตกต่างกัน เวลาคุณ merge มันเข้าด้วยกัน Git ก็ไม่รู้จะ merge มันเข้ามารวมกันเนียนๆได้ไง ถ้า fix ที่คุณทำไปบน issue #53 มีการแก้ส่วนเดียวกันบน file เดียวกันกับ `hotfix` คุณจะเจอ merge conflict ซึ่งมีหน้าตาประมาณนี้

	$ git merge iss53
	Auto-merging index.html
	CONFLICT (content): Merge conflict in index.html
	Automatic merge failed; fix conflicts and then commit the result.

Git ไม่ได้สร้าง merge commit อันใหม่ให้อัตโนมัติ มันกด pause เพื่อหยุดให้คุณ resolve merge conflict ถ้าคุณอยากดูว่า file ไหนบ้างที่ยังไม่ถูก merge ณ เวลาใดๆหลังจากเกิด merge conflict ก็สามารถดูได้ด้วยคำสั่ง `git status`:

	[master*]$ git status
	index.html: needs merge
	# On branch master
	# Changed but not updated:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#	unmerged:   index.html
	#

อะไรก็ตามที่มี merge conflict และยังไม่ถูก resolve จะถูก list ออกมาว่า unmerged Git จะเติม conflict-resolution markers ลงไปใน files ที่มี conflicts เพื่อคุณจะได้เปิดมันและ resolve conflicts เหล่านั้นได้ file ของคุณจะมี section ที่หน้าตาประมาณนี้

	<<<<<<< HEAD:index.html
	<div id="footer">contact : email.support@github.com</div>
	=======
	<div id="footer">
	  please contact us at support@github.com
	</div>
	>>>>>>> iss53:index.html

จากรูป version ที่ HEAD (ซึ่งก็คือ master branch เพราะว่านั่นคือที่ที่คุณ check out ออกมาตอนคุณ run merge command) จะเป็นส่วนบนสุดของ block นั้น (ไอ้ที่อยู่บน `=======` น่ะ) ขณะที่ version ที่อยู่ใน `iss53` branch จะอยู่ในส่วนล่าง ในการที่จะ resolve conflict คุณก็ต้องเลือกซักส่วน หรือไม่ก็ merge มันเข้าด้วยกันเอง ยกตัวอย่างเช่น คุณอาจจะ resolve conflict อันนี้โดยการแก้ทั้ง block ให้เป็นอย่างข้างล่าง

	<div id="footer">
	please contact us at email.support@github.com
	</div>

resolution (การซ่อม) อันนี้เอามาจากทั้งสองส่วนอย่างละนิดอย่างละหน่อย และผมก็ได้ลบไอ้พวก `<<<<<<<`, `=======` และ `>>>>>>>` ออกไป หลังจากคุณ resolved แต่ละ section ใน file ที่มี conflict แล้ว run `git add` บนแต่ละ file เพื่อระบุว่ามันถูก resolved การ Stage file เป็นการระบุว่ามันถูก resolved แล้วใน Git
ถ้าอยากใช้ graphical tool เพื่อ resolve issues เหล่านี้ ก็ run `git mergetool` ซึ่งจะเปิด visual merge tool แหล่มๆขึ้นมาและเรียงแต่ละ conflicts ขึ้นมาให้คุณแก้:

	$ git mergetool
	merge tool candidates: kdiff3 tkdiff xxdiff meld gvimdiff opendiff emerge vimdiff
	Merging the files: index.html

	Normal merge conflict for 'index.html':
	  {local}: modified
	  {remote}: modified
	Hit return to start merge resolution tool (opendiff):

ถ้า merge tool ที่ถูกเลือกขึ้นมาโดย default นั้นไม่โดนใจ (Git เลือก `opendiff` ให้ผมในกรณีนี้เพราะผม run command บน Mac) คุณก็ดูว่า supported tools ที่ list อยู่ข้างบนหลังจากคำว่า “merge tool candidates” แล้วพิมพ์ชื่อ tool ที่อยากใช้ลงไป ใน Chapter 7 เราจะมาถกกันว่าคุณจะเปลี่ยน default value เหล่านี้ใน environment คุณได้ยังไง

หลังจากปิด merge tool ไป Git ก็จะถามคุณว่า merge สำเร็จมัย? ถ้าคุณตอบไปว่าชิววว มันก็จะ stages file นั้นเพื่อระบุว่ามันถูก resolved แล้วให้คุณ

ลอง run `git status` อีกทีเพื่อเช็คดูว่าไม่เหลือ conflicts ให้ resolve แล้วนะ

	$ git status
	# On branch master
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#	modified:   index.html
	#

โอเค แหล่ม เช็คอีกทีให้มั่นใจว่าทุกอย่างที่มี conflicts ถูก staged แล้ว แล้วก็พิมพ์ `git commit` เพื่อจบการ merge commit โดยปรกติแล้ว commit message ตาม default จะหน้าตาประมาณนี้

	Merge branch 'iss53'

	Conflicts:
	  index.html
	#
	# It looks like you may be committing a MERGE.
	# If this is not correct, please remove the file
	# .git/MERGE_HEAD
	# and try again.
	#

คุณมาสามารถแก้เติมรายละเอียดลงไปว่าคุณ resolve merge อันนั้นได้ไงลงไปใน message ได้ถ้ารู้สึกว่ามันจะช่วยให้เพื่อนๆเข้าใจ merge นี้ง่ายขึ้นเวลาต้องกลับมาดูมันใหม่ในอนาคต (เช่นอธิบายว่าไอ้ที่ทำไปแบบนี้ ทำไปเพราะอะไร) แต่ถ้ามันชัดอยู่แล้วก็ไม่ต้อง

## การบริหาร Branch ##

เอาล่ะ หลังจากที่คุณ created, merged และ deleted บาง branches แล้ว มาดู tools ที่ใช้ทำ branch-management (บริหาร branche) บ้าง เพราะอาจจะต้องใช้เวลาที่คุณทำ branches เยอะๆ

ไอ้ command `git branch` ทำได้มากกว่าแค่ create หรือ delete branches ถ้าคุณ run มันโดยไม่ใส่ arguments คุณจะได้ list ของ branches ปัจจุบันที่คุณมีอยู่:

	$ git branch
	  iss53
	* master
	  testing

สังเกตุไอ้ `*` ข้างหน้า `master` branch มันจะคอยปะอยู่หน้า branch ที่คุณกำลัง checked out อยู่ในปัจจุบัน ซึ่งแปลว่า ถ้า commit ตอนนี้ `master` branch จะถูกขยับไปข้างหน้าด้วยงานที่เพิ่มมาใหม่ การจะดู commit สุดท้ายของแต่ละ branch คุณก็ run `git branch -v`:

	$ git branch -v
	  iss53   93b412c fix javascript issue
	* master  7a98805 Merge branch 'iss53'
	  testing 782fd34 add scott to the author list in the readmes

อีกทางหนึ่งที่ใช้ในการเช็ค state ของ branches คือการ list ดูว่ามี branch ไหนบ้างที่เคยถูก merge และ branch ไหนบ้างที่ยังไม่เคยถูก merge เข้ามายัง branch ที่คุณอยู่ option เทพที่ชื่อว่า `--merged` และ `--no-merged` ถูกเพิ่มเข้ามาใน Git ตั้งแต่ version 1.5.6 เพื่อประการนี้ การจะดูว่า branches ไหนบ้างที่เคยถูก merged เข้ามายัง branch ที่คุณอยู่ สามารถทำได้โดย run `git branch --merged`

	$ git branch --merged
	  iss53
	* master

เพราะคุณเคย merged branch `iss53` เข้ามาแล้วเมื่อตะกี้ คุณก็เลยเห็นมันอยู่ใน list ปรกติแล้ว branches ต่างๆใน list นี้ (ยกเว้นอันที่กา `*` ไว้ข้างหน้านะ) สามารถ delete ทิ้งได้ตามสบายด้วยคำสั่ง `git branch -d` เพราะคุณได้ย้ายงานที่ทำในนั้นไปไว้ที่ branch อื่นแล้ว เพราะฉะนั้นลบไปก็ไม่มีงานหาย

การจะดูว่า branches ไหนบ้างที่มีงานที่ยังไม่ได้ถูก merged เข้ามาก็สามารถทำได้โดยการ run `git branch --no-merged`:

	$ git branch --no-merged
	  testing

คำสั่งนี้แสดงอีก branch นึง เพราะว่ามันมีงานที่คุณยังไม่เคย merged เข้ามา ถ้าคุณลอง delete มันด้วยคำสั่ง `git branch -d` มันก็จะ fail

	$ git branch -d testing
	error: The branch 'testing' is not an ancestor of your current HEAD.
	If you are sure you want to delete it, run 'git branch -D testing'.

ถ้าอยากจะ delete branch นั้นไปพร้อมๆกับงานที่ทำอยู่ในนั้นจริงๆ ก็สามารถบังคับให้มันลบได้ด้วย option `-D` ดังที่ help message บอกไว้ข้างบน

## Workflows ของการแตก Branch ##

เอาหล่ะ หลังจากคุณมีเบสิคของการแตก branch และการ merge กลับเข้ามาติดตัวแล้ว มาดูกันว่าคุณสามารถ (และสมควร) ที่จะทำอะไรกับมันบ้าง? ใน section นี้ เราจะโชว์ workflows ทั่วไปว่าการแตก branch อันรวดเร็วส์นี้สามารถทำอะไรได้บ้าง คุณจะได้ตัดสินใจได้ว่าควรที่จะเอามันไปประยุกต์ใช้กับกระบวนการพัฒนาที่คุณใช้อยู่ไหม

### Branch ที่อายุยืนนาน ###

เพราะว่า Git มีท่า merge 3 ทาง การ merge จาก branch หนึ่งไปยังอีก branch หลายๆครั้งเป็นระยะเวลานานๆก็กลายเป็นเรื่องชิวๆไปโดยปริยาย นั่นแปลว่าคุณสามารถเปิดหลายๆ branches ทิ้งไว้สำหรับ stages ต่างๆในกระบวนการพัฒนาที่คุณใช้อยู่ และคุณก็สามารถ merge จากบาง branch ไปยัง branch อื่นๆได้อย่างสม่ำเสมอ

นักพัฒนาที่ใช้ Git หลายคนมี workflow ที่สนับสนุนวิธีแบบนี้ ยกตัวอย่างเช่น มี code stable สุดยิดเก็บไว้ใน `master` branch (ส่วนใหญ่จะเป็น code ที่ถูก release ไปแล้วเตรียมจะออก) แล้วพวกเขาก็มีอีก branch หนึ่ง เปิดควบคู่กันไปโดยตั้งชื่อมันว่า develop หรือ next แล้วก็ทำงานกันบนนี้ ไม่ก็ใช้เพื่อ test ความเสถียรของระบบ (ซึ่ง branch นี้ไม่จำเป็นต้อง stable ตลอดเวลา) แล้วเมื่อไหร่ก็ตามที่มันเริ่ม stable เขาก็ merge มันเข้า `master` ไอ้ develop branch นี้จะเป็นที่ไว้ pull พวก topic branches (พวก branch อายุสั้นๆอย่าง `iss53` branch อันก่อนหน้านี้) เข้ามาเมื่อมันเสร็จเพื่อเช็คว่ามัน pass ทุกๆ tests และไม่มี bugs ใหม่ๆโผล่เข้ามา

ในชีวิตจริงนั้น ถ้ามาดู pointers ที่ค่อยๆขยับขึ้นตามสายของ commits ของคุณ พวก branches ที่ stable จะอยู่ล่างๆใน commit history และพวก branches ที่เก็บของใหม่ๆแรงๆจะอยู่บนใน history (ดู Figure 3-18).

Insert 18333fig0318.png 
Figure 3-18. More stable branches are generally farther down the commit history.

It’s generally easier to think about them as work silos, where sets of commits graduate to a more stable silo when they’re fully tested (see Figure 3-19).

Insert 18333fig0319.png 
Figure 3-19. It may be helpful to think of your branches as silos.

You can keep doing this for several levels of stability. Some larger projects also have a `proposed` or `pu` (proposed updates) branch that has integrated branches that may not be ready to go into the `next` or `master` branch. The idea is that your branches are at various levels of stability; when they reach a more stable level, they’re merged into the branch above them.
Again, having multiple long-running branches isn’t necessary, but it’s often helpful, especially when you’re dealing with very large or complex projects.

### Topic Branches ###

Topic branches, however, are useful in projects of any size. A topic branch is a short-lived branch that you create and use for a single particular feature or related work. This is something you’ve likely never done with a VCS before because it’s generally too expensive to create and merge branches. But in Git it’s common to create, work on, merge, and delete branches several times a day.

You saw this in the last section with the `iss53` and `hotfix` branches you created. You did a few commits on them and deleted them directly after merging them into your main branch. This technique allows you to context-switch quickly and completely — because your work is separated into silos where all the changes in that branch have to do with that topic, it’s easier to see what has happened during code review and such. You can keep the changes there for minutes, days, or months, and merge them in when they’re ready, regardless of the order in which they were created or worked on.

Consider an example of doing some work (on `master`), branching off for an issue (`iss91`), working on it for a bit, branching off the second branch to try another way of handling the same thing (`iss91v2`), going back to your master branch and working there for a while, and then branching off there to do some work that you’re not sure is a good idea (`dumbidea` branch). Your commit history will look something like Figure 3-20.

Insert 18333fig0320.png 
Figure 3-20. Your commit history with multiple topic branches.

Now, let’s say you decide you like the second solution to your issue best (`iss91v2`); and you showed the `dumbidea` branch to your coworkers, and it turns out to be genius. You can throw away the original `iss91` branch (losing commits C5 and C6) and merge in the other two. Your history then looks like Figure 3-21.

Insert 18333fig0321.png 
Figure 3-21. Your history after merging in dumbidea and iss91v2.

It’s important to remember when you’re doing all this that these branches are completely local. When you’re branching and merging, everything is being done only in your Git repository — no server communication is happening.

## Remote Branches ##

Remote branches are references to the state of branches on your remote repositories. They’re local branches that you can’t move; they’re moved automatically whenever you do any network communication. Remote branches act as bookmarks to remind you where the branches on your remote repositories were the last time you connected to them.

They take the form `(remote)/(branch)`. For instance, if you wanted to see what the `master` branch on your `origin` remote looked like as of the last time you communicated with it, you would check the `origin/master` branch. If you were working on an issue with a partner and they pushed up an `iss53` branch, you might have your own local `iss53` branch; but the branch on the server would point to the commit at `origin/iss53`.

This may be a bit confusing, so let’s look at an example. Let’s say you have a Git server on your network at `git.ourcompany.com`. If you clone from this, Git automatically names it `origin` for you, pulls down all its data, creates a pointer to where its `master` branch is, and names it `origin/master` locally; and you can’t move it. Git also gives you your own `master` branch starting at the same place as origin’s `master` branch, so you have something to work from (see Figure 3-22).

Insert 18333fig0322.png 
Figure 3-22. A Git clone gives you your own master branch and origin/master pointing to origin’s master branch.

If you do some work on your local master branch, and, in the meantime, someone else pushes to `git.ourcompany.com` and updates its master branch, then your histories move forward differently. Also, as long as you stay out of contact with your origin server, your `origin/master` pointer doesn’t move (see Figure 3-23).

Insert 18333fig0323.png 
Figure 3-23. Working locally and having someone push to your remote server makes each history move forward differently.

To synchronize your work, you run a `git fetch origin` command. This command looks up which server origin is (in this case, it’s `git.ourcompany.com`), fetches any data from it that you don’t yet have, and updates your local database, moving your `origin/master` pointer to its new, more up-to-date position (see Figure 3-24).

Insert 18333fig0324.png 
Figure 3-24. The git fetch command updates your remote references.

To demonstrate having multiple remote servers and what remote branches for those remote projects look like, let’s assume you have another internal Git server that is used only for development by one of your sprint teams. This server is at `git.team1.ourcompany.com`. You can add it as a new remote reference to the project you’re currently working on by running the `git remote add` command as we covered in Chapter 2. Name this remote `teamone`, which will be your shortname for that whole URL (see Figure 3-25).

Insert 18333fig0325.png 
Figure 3-25. Adding another server as a remote.

Now, you can run `git fetch teamone` to fetch everything the remote `teamone` server has that you don’t have yet. Because that server is a subset of the data your `origin` server has right now, Git fetches no data but sets a remote branch called `teamone/master` to point to the commit that `teamone` has as its `master` branch (see Figure 3-26).

Insert 18333fig0326.png 
Figure 3-26. You get a reference to teamone’s master branch position locally.

### Pushing ###

When you want to share a branch with the world, you need to push it up to a remote that you have write access to. Your local branches aren’t automatically synchronized to the remotes you write to — you have to explicitly push the branches you want to share. That way, you can use private branches for work you don’t want to share, and push up only the topic branches you want to collaborate on.

If you have a branch named `serverfix` that you want to work on with others, you can push it up the same way you pushed your first branch. Run `git push (remote) (branch)`:

	$ git push origin serverfix
	Counting objects: 20, done.
	Compressing objects: 100% (14/14), done.
	Writing objects: 100% (15/15), 1.74 KiB, done.
	Total 15 (delta 5), reused 0 (delta 0)
	To git@github.com:schacon/simplegit.git
	 * [new branch]      serverfix -> serverfix

This is a bit of a shortcut. Git automatically expands the `serverfix` branchname out to `refs/heads/serverfix:refs/heads/serverfix`, which means, “Take my serverfix local branch and push it to update the remote’s serverfix branch.” We’ll go over the `refs/heads/` part in detail in Chapter 9, but you can generally leave it off. You can also do `git push origin serverfix:serverfix`, which does the same thing — it says, “Take my serverfix and make it the remote’s serverfix.” You can use this format to push a local branch into a remote branch that is named differently. If you didn’t want it to be called `serverfix` on the remote, you could instead run `git push origin serverfix:awesomebranch` to push your local `serverfix` branch to the `awesomebranch` branch on the remote project.

The next time one of your collaborators fetches from the server, they will get a reference to where the server’s version of `serverfix` is under the remote branch `origin/serverfix`:

	$ git fetch origin
	remote: Counting objects: 20, done.
	remote: Compressing objects: 100% (14/14), done.
	remote: Total 15 (delta 5), reused 0 (delta 0)
	Unpacking objects: 100% (15/15), done.
	From git@github.com:schacon/simplegit
	 * [new branch]      serverfix    -> origin/serverfix

It’s important to note that when you do a fetch that brings down new remote branches, you don’t automatically have local, editable copies of them. In other words, in this case, you don’t have a new `serverfix` branch — you only have an `origin/serverfix` pointer that you can’t modify.

To merge this work into your current working branch, you can run `git merge origin/serverfix`. If you want your own `serverfix` branch that you can work on, you can base it off your remote branch:

	$ git checkout -b serverfix origin/serverfix
	Branch serverfix set up to track remote branch refs/remotes/origin/serverfix.
	Switched to a new branch "serverfix"

This gives you a local branch that you can work on that starts where `origin/serverfix` is.

### Tracking Branches ###

Checking out a local branch from a remote branch automatically creates what is called a _tracking branch_. Tracking branches are local branches that have a direct relationship to a remote branch. If you’re on a tracking branch and type `git push`, Git automatically knows which server and branch to push to. Also, running `git pull` while on one of these branches fetches all the remote references and then automatically merges in the corresponding remote branch.

When you clone a repository, it generally automatically creates a `master` branch that tracks `origin/master`. That’s why `git push` and `git pull` work out of the box with no other arguments. However, you can set up other tracking branches if you wish — ones that don’t track branches on `origin` and don’t track the `master` branch. The simple case is the example you just saw, running `git checkout -b [branch] [remotename]/[branch]`. If you have Git version 1.6.2 or later, you can also use the `--track` shorthand:

	$ git checkout --track origin/serverfix
	Branch serverfix set up to track remote branch refs/remotes/origin/serverfix.
	Switched to a new branch "serverfix"

To set up a local branch with a different name than the remote branch, you can easily use the first version with a different local branch name:

	$ git checkout -b sf origin/serverfix
	Branch sf set up to track remote branch refs/remotes/origin/serverfix.
	Switched to a new branch "sf"

Now, your local branch sf will automatically push to and pull from origin/serverfix.

### Deleting Remote Branches ###

Suppose you’re done with a remote branch — say, you and your collaborators are finished with a feature and have merged it into your remote’s `master` branch (or whatever branch your stable codeline is in). You can delete a remote branch using the rather obtuse syntax `git push [remotename] :[branch]`. If you want to delete your `serverfix` branch from the server, you run the following:

	$ git push origin :serverfix
	To git@github.com:schacon/simplegit.git
	 - [deleted]         serverfix

Boom. No more branch on your server. You may want to dog-ear this page, because you’ll need that command, and you’ll likely forget the syntax. A way to remember this command is by recalling the `git push [remotename] [localbranch]:[remotebranch]` syntax that we went over a bit earlier. If you leave off the `[localbranch]` portion, then you’re basically saying, “Take nothing on my side and make it be `[remotebranch]`.”

## Rebasing ##

In Git, there are two main ways to integrate changes from one branch into another: the `merge` and the `rebase`. In this section you’ll learn what rebasing is, how to do it, why it’s a pretty amazing tool, and in what cases you won’t want to use it.

### The Basic Rebase ###

If you go back to an earlier example from the Merge section (see Figure 3-27), you can see that you diverged your work and made commits on two different branches.

Insert 18333fig0327.png 
Figure 3-27. Your initial diverged commit history.

The easiest way to integrate the branches, as we’ve already covered, is the `merge` command. It performs a three-way merge between the two latest branch snapshots (C3 and C4) and the most recent common ancestor of the two (C2), creating a new snapshot (and commit), as shown in Figure 3-28.

Insert 18333fig0328.png 
Figure 3-28. Merging a branch to integrate the diverged work history.

However, there is another way: you can take the patch of the change that was introduced in C3 and reapply it on top of C4. In Git, this is called _rebasing_. With the `rebase` command, you can take all the changes that were committed on one branch and replay them on another one.

In this example, you’d run the following:

	$ git checkout experiment
	$ git rebase master
	First, rewinding head to replay your work on top of it...
	Applying: added staged command

It works by going to the common ancestor of the two branches (the one you’re on and the one you’re rebasing onto), getting the diff introduced by each commit of the branch you’re on, saving those diffs to temporary files, resetting the current branch to the same commit as the branch you are rebasing onto, and finally applying each change in turn. Figure 3-29 illustrates this process.

Insert 18333fig0329.png 
Figure 3-29. Rebasing the change introduced in C3 onto C4.

At this point, you can go back to the master branch and do a fast-forward merge (see Figure 3-30).

Insert 18333fig0330.png 
Figure 3-30. Fast-forwarding the master branch.

Now, the snapshot pointed to by C3' is exactly the same as the one that was pointed to by C5 in the merge example. There is no difference in the end product of the integration, but rebasing makes for a cleaner history. If you examine the log of a rebased branch, it looks like a linear history: it appears that all the work happened in series, even when it originally happened in parallel.

Often, you’ll do this to make sure your commits apply cleanly on a remote branch — perhaps in a project to which you’re trying to contribute but that you don’t maintain. In this case, you’d do your work in a branch and then rebase your work onto `origin/master` when you were ready to submit your patches to the main project. That way, the maintainer doesn’t have to do any integration work — just a fast-forward or a clean apply.

Note that the snapshot pointed to by the final commit you end up with, whether it’s the last of the rebased commits for a rebase or the final merge commit after a merge, is the same snapshot — it’s only the history that is different. Rebasing replays changes from one line of work onto another in the order they were introduced, whereas merging takes the endpoints and merges them together.

### More Interesting Rebases ###

You can also have your rebase replay on something other than the rebase branch. Take a history like Figure 3-31, for example. You branched a topic branch (`server`) to add some server-side functionality to your project, and made a commit. Then, you branched off that to make the client-side changes (`client`) and committed a few times. Finally, you went back to your server branch and did a few more commits.

Insert 18333fig0331.png 
Figure 3-31. A history with a topic branch off another topic branch.

Suppose you decide that you want to merge your client-side changes into your mainline for a release, but you want to hold off on the server-side changes until it’s tested further. You can take the changes on client that aren’t on server (C8 and C9) and replay them on your master branch by using the `--onto` option of `git rebase`:

	$ git rebase --onto master server client

This basically says, “Check out the client branch, figure out the patches from the common ancestor of the `client` and `server` branches, and then replay them onto `master`.” It’s a bit complex; but the result, shown in Figure 3-32, is pretty cool.

Insert 18333fig0332.png 
Figure 3-32. Rebasing a topic branch off another topic branch.

Now you can fast-forward your master branch (see Figure 3-33):

	$ git checkout master
	$ git merge client

Insert 18333fig0333.png 
Figure 3-33. Fast-forwarding your master branch to include the client branch changes.

Let’s say you decide to pull in your server branch as well. You can rebase the server branch onto the master branch without having to check it out first by running `git rebase [basebranch] [topicbranch]` — which checks out the topic branch (in this case, `server`) for you and replays it onto the base branch (`master`):

	$ git rebase master server

This replays your `server` work on top of your `master` work, as shown in Figure 3-34.

Insert 18333fig0334.png 
Figure 3-34. Rebasing your server branch on top of your master branch.

Then, you can fast-forward the base branch (`master`):

	$ git checkout master
	$ git merge server

You can remove the `client` and `server` branches because all the work is integrated and you don’t need them anymore, leaving your history for this entire process looking like Figure 3-35:

	$ git branch -d client
	$ git branch -d server

Insert 18333fig0335.png 
Figure 3-35. Final commit history.

### The Perils of Rebasing ###

Ahh, but the bliss of rebasing isn’t without its drawbacks, which can be summed up in a single line:

**Do not rebase commits that you have pushed to a public repository.**

If you follow that guideline, you’ll be fine. If you don’t, people will hate you, and you’ll be scorned by friends and family.

When you rebase stuff, you’re abandoning existing commits and creating new ones that are similar but different. If you push commits somewhere and others pull them down and base work on them, and then you rewrite those commits with `git rebase` and push them up again, your collaborators will have to re-merge their work and things will get messy when you try to pull their work back into yours.

Let’s look at an example of how rebasing work that you’ve made public can cause problems. Suppose you clone from a central server and then do some work off that. Your commit history looks like Figure 3-36.

Insert 18333fig0336.png 
Figure 3-36. Clone a repository, and base some work on it.

Now, someone else does more work that includes a merge, and pushes that work to the central server. You fetch them and merge the new remote branch into your work, making your history look something like Figure 3-37.

Insert 18333fig0337.png 
Figure 3-37. Fetch more commits, and merge them into your work.

Next, the person who pushed the merged work decides to go back and rebase their work instead; they do a `git push --force` to overwrite the history on the server. You then fetch from that server, bringing down the new commits.

Insert 18333fig0338.png 
Figure 3-38. Someone pushes rebased commits, abandoning commits you’ve based your work on.

At this point, you have to merge this work in again, even though you’ve already done so. Rebasing changes the SHA-1 hashes of these commits so to Git they look like new commits, when in fact you already have the C4 work in your history (see Figure 3-39).

Insert 18333fig0339.png 
Figure 3-39. You merge in the same work again into a new merge commit.

You have to merge that work in at some point so you can keep up with the other developer in the future. After you do that, your commit history will contain both the C4 and C4' commits, which have different SHA-1 hashes but introduce the same work and have the same commit message. If you run a `git log` when your history looks like this, you’ll see two commits that have the same author date and message, which will be confusing. Furthermore, if you push this history back up to the server, you’ll reintroduce all those rebased commits to the central server, which can further confuse people.

If you treat rebasing as a way to clean up and work with commits before you push them, and if you only rebase commits that have never been available publicly, then you’ll be fine. If you rebase commits that have already been pushed publicly, and people may have based work on those commits, then you may be in for some frustrating trouble.

## Summary ##

We’ve covered basic branching and merging in Git. You should feel comfortable creating and switching to new branches, switching between branches and merging local branches together.  You should also be able to share your branches by pushing them to a shared server, working with others on shared branches and rebasing your branches before they are shared.
