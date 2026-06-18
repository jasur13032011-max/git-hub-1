# git-hub-1
Git-da merge va rebase bilan ishlash, konfliktlarni yechish va interaktiv rebase (interactive rebase) bo'yicha to'liq qo'llanma va kunlik workflow (ish tartibi) hujjati.

1. Bir xil senariyni avval merge bilan, keyin rebase bilan hal qilish
Faraz qilaylik, bizda main va feature nomli branch'lar bor. Har ikkala branch'da ham o'zgarishlar kiritilgan.

Senariyni tayyorlash
Bash
git checkout main
# main branch'da o'zgarish qilamiz
echo "Main o'zgarishi" >> file.txt
git commit -am "Main branch'da o'zgarish"

git checkout feature
# feature branch'da o'zgarish qilamiz
echo "Feature o'zgarishi" >> file.txt
git commit -am "Feature branch'da o'zgarish"
Variant A: merge orqali birlashtirish
merge yangi maxsus "merge commit" yaratadi va har ikkala branch tarixini saqlab qoladi.

Bash
git checkout main
git merge feature
# Agar konflikt bo'lsa, hal qilinadi va commit qilinadi.
Variant B: rebase orqali birlashtirish
rebase bizning feature branch'imizdagi commit'larni olib, main branch'ining eng oxirgi commit'i ustiga chiziqli qilib ko'chirib qo'yadi (tarixni qayta yozadi).

Bash
git checkout feature
git rebase main
# Endi main'ga o'tib, tezkor birlashtiramiz (fast-forward):
git checkout main
git merge feature
2. Tarix grafiklarini taqqoslash (--graph --all)
Tarix qanday ko'rinishda ekanini tekshirish uchun quyidagi buyruqdan foydalanamiz:

Bash
git log --oneline --graph --all
merge dan keyingi grafik: Tarmoqlangan va qayta birlashgan chiziqlarni ko'rasiz. Tarixda aniq "shoxlanish" ko'rinib turadi.

rebase dan keyingi grafik: Mutlaqo chiziqli (bitta tekis chiziq) tarixni ko'rasiz. Go'yoki hamma ishlar bitta branch'da ketma-ket bajarilganga o'xshaydi.

3. Rebase konfliktini yechish (--continue, --abort)
Rebase jarayonida konflikt (ziddiyat) yuzaga kelsa, Git jarayonni to'xtatadi. Uni hal qilish uchun quyidagi qadamlar bajariladi:

Konflikt bor fayllarni ochib, <<<<<<<, =======, >>>>>>> belgilarini to'g'rilab, kodni holatiga keltiring.

O'zgarishlarni keshga qo'shing:

Bash
git add <fayl_nomi>
Davom ettirish: Rebase jarayonini davom ettirish buyrug'ini bering:

Bash
git rebase --continue
Bekor qilish: Agar adashib ketganingizni bilsangiz va hammasini rebase'dan oldingi holatga qaytarmoqchi bo'lsangiz:

Bash
git rebase --abort
4. Interaktiv Rebase (Interactive Rebase) amallari
Interaktiv rebase oxirgi N ta commit tarixini o'zgartirish uchun ishlatiladi. Buyruq: git rebase -i HEAD~N (masalan, oxirgi 3 ta commit uchun HEAD~3).

Buyruq berilgach, matn muharriri ochiladi va commit'lar ro'yxati oldida pick so'zi turadi.

A. 3 ta commit'ni squash bilan 1 ga birlashtirish
Bir nechta mayda commit'larni bitta mazmunli commit'ga aylantirish:

Plaintext
pick a1b2c3d Birinchi mayda o'zgarish
squash e4f5g6h Ikkinchi mayda o'zgarish
squash i8j9k0l Uchinchi mayda o'zgarish
Faylni saqlab yopsangiz, Git sizdan 3 ta commit uchun umumiy yangi xabar yozishni so'raydi.

B. reword (Xabar tuzatish)
Faqatgina commit xabarini (matnini) o'zgartirish:

Plaintext
reword a1b2c3d Imlo xatosi bor commit xabari
pick e4f5g6h Keyingi commit
Fayl saqlangach, yangi xabarni yozish uchun muharrir ochiladi.

C. drop (Commit'ni o'chirish)
Keraksiz commit'ni butunlay tarixdan o'chirib tashlash:

Plaintext
drop a1b2c3d Bu commit keraksiz, o'chirilsin
pick e4f5g6h Bu saqlansin
(Yoki shunchaki o'sha commit qatorini fayldan o'chirib tashlasangiz ham bo'ladi).

D. reorder (Qatorlarni almashtirish)
Commit'larning ketma-ketlik vaqtini (tartibini) o'zgartirish:

Plaintext
# Fayldagi qatorlar o'rnini shunchaki almashtirasiz:
pick e4f5g6h Bu commit aslida keyin edi, endi birinchi bo'ladi
pick a1b2c3d Bu commit birinchi edi, endi keyin bo'ladi
5. git pull --rebase ishlatish
Oddiy git pull buyrug'i git fetch va git merge amallarini bajaradi, bu esa ortiqcha merge commit'lar ko'payishiga olib keladi.

Bash
git pull --rebase
Vazifasi: Serverdagi (origin) yangi o'zgarishlarni yuklab oladi va sizning lokal branch'ingizdagi hali push qilinmagan commit'larni o'sha yangi o'zgarishlarning ustiga chiziqli qilib joylashtiradi. Tarix toza qoladi.

6. git push --force-with-lease xavfsiz versiyasi
Agar siz local tariqni o'zgartirsangiz (rebase yoki squash qilsangiz), uni serverga oddiy push qilib bo'lmaydi. --force ishlatish kerak bo'ladi. Biroq, oddiy --force (yoki -f) xavfli, chunki u jamoadoshingiz serverga yozgan yangi kodlarni o'chirib yuborishi mumkin.

Bash
git push origin feature-branch --force-with-lease
Nega xavfsiz? Agar siz oxirgi marta fetch qilganingizdan keyin serverga kimdir boshqa kod push qilgan bo'lsa, --force-with-lease push qilishni rad etadi va jamoa a'zosining kodi o'chib ketishidan himoya qiladi.

7. Push qilingan commit'da rebase xavfi — Hisobot
Oltin Qoida: Hech qachon umumiy (shared/public) yoki main branch'ga push qilib bo'lingan commit'larni rebase qilmang.

Xavflar va oqibatlar:
Tarixning buzilishi: Rebase mavjud commit'larning ID raqamlarini (SHA-1 hash) o'zgartiradi. Agar boshqa dasturchilar u commit'larni tortib olgan bo'lsa, ularning tarixi sizniki bilan mos kelmay qoladi.

"Duplicated" Commit'lar: Hamkasblaringiz git pull qilganda, Git eski va yangi (rebase bo'lgan) commit'larni birlashtirishga harakat qiladi va bitta ishning ikki xil commit'i paydo bo'ladi.

Konfliktlar to'lqini: Butun jamoa tushunarsiz va qiyin yechiladigan konfliktlar girdobiga tushib qoladi.

8. Kunlik Workflow yozilgan dokument
Quyida jamoada ishlash uchun tavsiya etiladigan "Toza tarix (Clean History)" tamoyiliga asoslangan kunlik ish tartibi keltirilgan:

📄 DAILY GIT WORKFLOW DOCUMENT
1. Kun boshida (Lokal bazani yangilash)
Ishni boshlashdan oldin main branch'dagi oxirgi o'zgarishlarni olamiz:

Bash
git checkout main
git pull origin main
2. Yangi vazifa boshlash
Har bir vazifa (task) uchun alohida branch ochiladi:

Bash
git checkout -b feature/task-123
3. Kod yozish va lokal commit'lar
Kun davomida kichik va tez-tez commit qilib boriladi (muammolarni oson kuzatish uchun):

Bash
git add .
git commit -m "WIP: logika qismi yozildi"
4. Server bilan sinxronizatsiya (Kun davomida yoki yakunida)
Biz kod yozguncha main branch'ga boshqa dasturchilar kod qo'shgan bo'lishi mumkin. O'z branch'imizni yangilaymiz:

Bash
git checkout feature/task-123
git fetch origin
git rebase origin/main
# Agar konflikt chiqsa, yechib `git rebase --continue` qilinadi
5. Kodni tartibga solish (Pull Request'dan oldin)
PR (Pull Request) ochishdan oldin oxirgi 3-4 ta mayda commit'larni bitta chiroyli commit'ga birlashtiramiz:

Bash
git rebase -i HEAD~3
# `squash` amali orqali commit'lar birlashtiriladi
6. Serverga yuklash va Xavfsiz Force-Push
Tarix o'zgargani uchun xavfsiz kalit bilan serverga yuklaymiz:

Bash
git push origin feature/task-123 --force-with-lease
7. Pull Request va Birlashtirish
GitHub/GitLab'da Pull Request ochiladi. Kod tekshirilgach (Review), main branch'ga Merge yoki Squash and Merge qilinadi.
