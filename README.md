# git-hub-2
Git-ning "sehrli" va eng kuchli vositalari hisoblangan stash, cherry-pick, amend va aliases tushunchalari loyihani boshqarishni yangi bosqichga olib chiqadi. Quyida har bir buyruqning batafsil amaliy qo'llanmasi keltirilgan.

1. git stash — Ishni Vaqtincha Yashirish
git stash joriy ishchi papkadagi o'zgarishlarni (hali commit qilinmagan kodlarni) vaqtincha xotiraga olib, ishchi holatni toza (oxirgi commit holatiga) keltirish uchun ishlatiladi.

A) 3 ta Haqiqiy Vaziyat:
Yarim qolgan ish (Context Switch): Biror funksiya ustida ishlayapsiz, kod hali tayyor emas (chala). Shu paytda favqulodda jiddiy xatoni (hotfix) tuzatish topshirig'i keldi. Chala kodni commit qilmaslik uchun uni stash qilib turasiz.

Pull Conflict oldini olish: Masofaviy repozitiyadan git pull qilmoqchisiz, lekin siz o'zgartirgan fayllar serverda ham o'zgargan bo'lsa, Git pull qilishga yo'l qo'ymaydi. O'zgarishlaringizni stash qilib, pull qilib, keyin o'zgarishlarni qaytarasiz.

Eksperiment (Tajriba): Kodga biror yangi g'oyani sinab ko'rmoqchisiz, lekin u o'xshamadimi yoki yo'qmi aniqmas. Asosiy kodni buzmaslik uchun joriy o'zgarishlarni saqlab qo'yib, toza kodda eksperiment qilsa bo'ladi.

B) Stash Buyruqlari:
git stash list — Xotirada saqlangan barcha stashlar ro'yxatini ko'rsatadi (stash@{0}, stash@{1}).

git stash apply — Stashdagi o'zgarishlarni kodga qaytaradi, lekin stashni ro'yxatdan o'chirmaydi.

git stash pop — Stashdagi o'zgarishlarni kodga qaytaradi va uni ro'yxatdan o'chirib tashlaydi.

git stash drop stash@{0} — Muayyan bir stashni ro'yxatdan butunlay o'chiradi.

C) Untracked fayllar bilan ishlash (git stash -u)
Standart git stash faqat Git nazoratida bo'lgan (tracked) fayllarni yashiradi. Yangi yaratilgan va hali git add qilinmagan fayllarni ham yashirish uchun -u (--include-untracked) flagi qo'shiladi:

Bash
git stash -u -m "feat: yangi komponent va chala kodlar"
D) Stash ichidan yangi branch ochish (git stash branch)
Agar stashda uzoq muddat qolib ketgan o'zgarishlar bo'lsa va ularni joriy branchga qo'shish ko'plab konfliktlar keltirib chiqarishi mumkin bo'lsa, o'sha stash asosida mutlaqo yangi branch ochish mumkin:

Bash
git stash branch feature-from-stash stash@{0}
Bu buyruq yangi branch ochadi, stashni o'sha branchga qo'llaydi va muvaffaqiyatli bo'lsa stashni o'chirib yuboradi.

2. git cherry-pick — Kerakli Commitni Sug'urib Olish
Boshqa branchdagi barcha o'zgarishlarni emas, balki faqat bitta yoki bir nechta aniq commitni joriy branchga nusxalab o'tkazish uchun ishlatiladi.

A) Bitta va bir nechta commitni cherry-pick qilish:
Bitta commit: ```bash
git cherry-pick abc1234

Bir nechta alohida commitlar:

Bash
git cherry-pick abc1234 def5678
Commitlar diapazoni (A dan B gacha):

Bash
git cherry-pick abc1234..g7h8i9j
B) Cherry-pick Konfliktini Yechish:
Agar o'tkazilayotgan commit joriy branchdagi kod bilan to'qnashsa, konflikt yuzaga keladi va Git jarayonni to'xtatadi. Uni yechish ketma-ketligi:

Fayllarga kirib, konflikt markerlarini (<<<<<<<, =======, >>>>>>>) o'chirib, kodni to'g'rilang.

To'g'rilangan fayllarni staging area'ga qo'shing: git add fayl.js

Cherry-pick jarayonini davom ettiring:

Bash
git cherry-pick --continue
(Agar cherry-pickni bekor qilmoqchi bo'lsangiz: git cherry-pick --abort)

3. git commit --amend — Oxirgi Commitni Tahrirlash
A) Commit xabarini tahrirlash:
Agar oxirgi commit xabarida imlo xatosi ketgan bo'lsa yoki noto'g'ri matn yozilgan bo'lsa:

Bash
git commit --amend -m "fix: to'g'ri va mukammal commit matni"
B) Yangi fayl yoki o'zgarish qo'shish (--no-edit):
Agar commit qilib bo'gach, qaysidir faylni qo'shish esdan chiqqanini payqab qolsangiz, yangi commit yaratmasdan uni oxirgisiga qo'shib yuborish mumkin:

Bash
git add unutilgan-fayl.js
git commit --amend --no-edit
--no-edit flagi commit matnini o'zgartirmasdan, shunchaki yangi faylni oxirgi commit ichiga joylashtiradi.

C) ⚠️ PUSH qilingan commitda AMEND qilish xavfi:
Agar siz commitni GitHub/GitLab-ga push qilib bo'lgan bo'lsangiz, uni hech qachon localda amend qilmasligingiz kerak.

Sababi: amend buyrug'i eski commitni shunchaki tahrirlamaydi, balki uni butunlay o'chirib, o'rniga yangi hash ID li mutlaqo yangi commit yaratadi.

Agar push qilingan commitni amend qilib, qayta majburlab push (--force) qilsangiz, ushbu repozitoriyda ishlayotgan boshqa jamoa a'zolarining Git tarixi buziladi (git pull qilganda og'ir konfliktlar yuzaga keladi).

4. Unumdorlikni Oshiruvchi 6 ta Foydali Git Alias
Git buyruqlarini qisqartma shaklga keltirish orqali vaqtni sezilarli darajada tejash mumkin. Terminalda quyidagi konfiguratsiyalarni ishga tushiring:

Bash
# 1. git st -> git status
git config --global alias.st status

# 2. git co -> git checkout
git config --global alias.co checkout

# 3. git cm -> Conventional commit uchun qisqartma
git config --global alias.cm "commit -m"

# 4. git br -> Branchlar ro'yxati
git config --global alias.br branch

# 5. git sh -> Oxirgi chala ishlarni tezda yashirish (untracked fayllar bilan)
git config --global alias.sh "stash -u -m"

# 6. git lg -> Chiroyli, rangli va grafik shakldagi log tarixi
git config --global alias.lg "log --graph --oneline --decorate --all"
Endi uzoq buyruqlar o'rniga, masalan, shunchaki git lg yoki git st deb yozish kifoya qiladi.

Keyingi darslarimiz yoki amaliy mashg'ulotlar uchun qaysi Git mavzusini chuqurroq o'rganishni istaysiz (masalan: git rebase yoki jamoada ishlash workflow'lari)?
