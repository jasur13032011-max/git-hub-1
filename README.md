# git-hub-2
Git — zamonaviy dasturlashda versiyalarni boshqarish tizimi (VCS) bo'lib, loyihadagi o'zgarishlarni bosqichma-bosqich yozib borish uchun xizmat qiladi. Quyida keltirilgan barcha Git buyruqlari va qadamlari amaliy ketma-ketlikda ko'rsatilgan.

1. Global sozlamalarni o'rnatish (git config)
Git-da birinchi marta ishlashdan oldin commit (o'zgarishlar) kim tomonidan qilinganini bilish uchun ism va elektron pochtani sozlash shart:

Bash
git config --global user.name "Sardor Rahimov"
git config --global user.email "sardor@example.com"
2. Yangi loyiha ochish va uni faollashtirish (git init)
Yangi papka ochamiz, uning ichiga kiramiz va bo'sh Git repozitoriysini ishga tushiramiz:

Bash
mkdir my-awesome-app
cd my-awesome-app
git init
Natija: Papka ichida yashirin .git qoplami ochiladi. Bu Git barcha o'zgarishlarni yozib boradigan "miya" hisoblanadi.

3. Ataylab xato: git add qilmasdan git commit yozish
Git qanday ishlashini tushunish uchun yangi index.html faylini ochamiz va uni to'g'ridan-to'g'ri commit qilishga urinamiz:

Bash
touch index.html
git commit -m "feat: yangi bosh sahifa qo'shildi"
Natija (Xatolik xabari):

Plaintext
On branch main
Untracked files:
  index.html

nothing added to commit but untracked files present
Sababi va tushuntirish: Git tizimida Staging Area (Kutish zal) tushunchasi bor. Fayl yaratilgach, u avval git add buyrug'i orqali kutish zaliga o'tkazilishi shart. Git loyihadagi har bir faylni o'z-o'zidan commit-ga qo'shmaydi. Biz unga qaysi fayllarni rasmga (commit) tushirish kerakligini git add orqali aytishimiz kerak.

4. Ketma-ket 5 ta Commit (Conventional Commits formatida)
Conventional Commits — dasturchilar orasidagi standart bo'lib, commit matnidan loyihada nima o'zgarganini darrov bilish imkonini beradi. Odatda quyidagi turlardan foydalaniladi:

feat: Yangi funksiya (feature) qo'shilganda.

fix: Xatolik (bug) tuzatilganda.

docs: Faqat hujjatlar (README, dokumentatsiya) o'zgarganda.

chore: Kodga aloqador bo'lmagan mayda ishlar (paket o'rnatish, sozlamalar).

Keling, ketma-ketlikda o'zgarishlar kiritib, 5 ta har xil turdagi commit yozamiz:

Commit 1 (feat) - HTML faylni qo'shish
Bash
git add index.html
git commit -m "feat: bosh sahifa uchun boshlang'ich HTML struktura"
Commit 2 (docs) - README fayli
Bash
touch README.md
echo "# Mening Loyiham" >> README.md
git add README.md
git commit -m "docs: loyihani ishga tushirish bo'yicha yo'riqnoma qo'shildi"
Commit 3 (chore) - Git ignore yoki sozlamalar
Bash
touch .gitignore
echo "node_modules/" >> .gitignore
git add .gitignore
git commit -m "chore: .gitignore fayli yaratildi va keraksiz qoplamalar kiritildi"
Commit 4 (feat) - Yangi JS logikasi
Bash
touch app.js
echo "console.log('Salom');" >> app.js
git add app.js
git commit -m "feat: foydalanuvchini salomlash funksiyasi yozildi"
Commit 5 (fix) - Koddagi xatoni tuzatish
Bash
echo "console.log('Salom Dunyo!');" > app.js
git add app.js
git commit -m "fix: salomlashish matnidagi imlo xatosi tuzatildi"
5. Multi-line Commit yozish (-m ni ikki marta ishlatish)
Ba'zan commit sarlavhasidan tashqari, uning batafsil tavsifini (body) ham yozish kerak bo'ladi. Buning uchun terminalda -m flagini ketma-ket yozish mumkin:

Bash
echo "/* CSS styles */" > style.css
git add style.css
git commit -m "feat: sahifa uchun asosiy CSS stillar qo'shildi" -m "Batafsil: Tugmalar ranglari va responsive grid tizimi yozildi, ranglar WCAG standartlariga moslandi."
6. Tarixni ko'rish (git log --oneline)
Barcha qilingan ishlarni ixcham, bir qatorli ko'rinishda tekshiramiz. Bu terminaldagi tarixning simulyatsiyasi (screenshot o'rniga):

Bash
git log --oneline
Terminaldagi natija:

Plaintext
a1b2c3d (HEAD -> main) feat: sahifa uchun asosiy CSS stillar qo'shildi
f5e4d3c fix: salomlashish matnidagi imlo xatosi tuzatildi
b9e8d7c feat: foydalanuvchini salomlash funksiyasi yozildi
c6b5a4f chore: .gitignore fayli yaratildi va keraksiz qoplamalar kiritildi
d3c2b1a docs: loyihani ishga tushirish bo'yicha yo'riqnoma qo'shildi
e9f8a7b feat: bosh sahifa uchun boshlang'ich HTML struktura
(Chap tomondagi 7 ta belgili kodlar — har bir commit-ning unikal ID (hash) raqamlaridir).

7. Commit tafsilotlarini tekshirish (git show)
Muayyan bitta commit ichida aynan qaysi qatorlar o'zgarganini batafsil ko'rish uchun git show buyrug'iga commit-ning boshidagi ID raqami beriladi (masalan, yuqoridagi ro'yxatdan f5e4d3c ni olamiz):

Bash
git show f5e4d3c
Terminaldagi natija:

Plaintext
commit f5e4d3c2b1a3f4e56789abcdef0123456789
Author: Sardor Rahimov <sardor@example.com>
Date:   Thu Jul 9 11:52:38 2026 +0500

    fix: salomlashish matnidagi imlo xatosi tuzatildi

diff --git a/app.js b/app.js
index e69de29..d123456 100644
--- a/app.js
+++ b/app.js
@@ -1 +1 @@
-console.log('Salom');
+console.log('Salom Dunyo!');
Tushuntirish: git show natijasida qizil rangda o'chirilgan eski qator (-console.log('Salom');) va yashil rangda qo'shilgan yangi qator (+console.log('Salom Dunyo!');) aniq ko'rinadi.
