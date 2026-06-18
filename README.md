# git-hub-2
Git-da xatolarni tuzatish, o'zgarishlarni orqaga qaytarish (reset, revert), yo'qolgan commit'larni topish (reflog) va keraksiz fayllarni tozalash bo'yicha to'liq amaliy qo'llanma.

1. git reset --soft (Commit'ni ochish)
--soft buyrug'i oxirgi commit(lar)ni o'chiradi (bekor qiladi), lekin u commit ichidagi yozilgan kodlarni butunlay saqlab qoladi va ularni Staging Area (git add qilingan holat)ga qaytaradi.

Bash
# Oxirgi 1 ta commit'ni bekor qilish
git reset --soft HEAD~1
Qachon kerak? Commit xabarini noto'g'ri yozsangiz yoki commit'ga yana bir nechta faylni qo'shish esdan chiqqan bo'lsa. Kod o'chmaydi, shunchaki commit ochiladi.

2. git reset --mixed (Default holat)
Agar git reset buyrug'iga hech qanday kalit so'z bermasangiz, u sukut bo'yicha --mixed rejimida ishlaydi. U commit'ni o'chiradi, lekin o'zgarishlarni Working Directory (ishchi joy, ya'ni git add qilinmagan holat)ga qaytaradi.

Bash
# default rejim: oxirgi commit'ni ochish va staging'dan chiqarish
git reset HEAD~1
Qachon kerak? Qilingan o'zgarishlar qolishi kerak, lekin ularni qaytadan tartiblab, bo'lib-bo'lib commit qilmoqchi bo'lsangiz.

3. git reset --hard xavfli misol + reflog bilan qutqarish
--hard eng xavfli buyruq hisoblanadi, chunki u commit'ni ham, kiritilgan o'zgarishlarni ham butunlay o'chirib yuboradi.

Xavfli misol:
Bash
# Oxirgi commit va undagi barcha kodlarni butunlay o'chirish
git reset --hard HEAD~1
Agar buni adashib yozib yuborsangiz, oxirgi yozgan kodlaringiz g'oyib bo'ladi.

reflog yordamida qutqarish:
Git siz o'chirdi deb o'ylagan commit'larni darhol bazadan o'chirib yubormaydi. Ularni reflog orqali topish mumkin:

Bash
# 1. Hamma harakatlar tarixini ko'ramiz
git reflog
Chiquvchi ro'yxatdan o'chib ketgan commit'ni topamiz (masalan, HEAD@{1} o'chirishdan oldingi holat bo'ladi):

Plaintext
7a3b2c1 HEAD@{0}: reset: moving to HEAD~1
a1b2c3d HEAD@{1}: commit: Muhim funksiya yozilgandi (Mana shu bizga kerak!)
Bash
# 2. O'sha commit'ga qaytamiz va hamma narsani tiklaymiz
git reset --hard a1b2c3d
4. git revert bitta commit
revert buyrug'i resetdan farqli o'laroq tarixni o'chirmaydi. U ko'rsatilgan commit'ning teskari o'zgarishini qiluvchi mutlaqo yangi commit yaratadi.

Bash
# a1b2c3d commit'ida nima yozilgan bo'lsa, o'shani teskarisini qilib yangi commit yaratadi
git revert a1b2c3d
Qachon kerak? Agar xato kodingiz allaqachon serverga (remote) push qilingan bo'lsa, tarixni buzmaslik uchun faqat revert ishlatiladi.

5. git revert bir nechta commit
Agar bir nechta commit'larni ketma-ketlikda bekor qilish kerak bo'lsa, oraliqni ko'rsatish mumkin.

Bash
# Oldingi 3 ta commit'ni teskari o'zgarish bilan qaytarish (lekin commit qilmasdan turish)
git revert -n HEAD~3..HEAD
-n yoki --no-commit kaliti har bitta commit uchun alohida-alohida revert-commit yaratish o'rniga, barcha teskari o'zgarishlarni yig'ib, bitta yakuniy commit qilish imkonini beradi.

6. git reflog bilan tarix kuzatish
git log faqat joriy branch'dagi commit'lar tarixini ko'rsatsa, git reflog siz lokal kompyuterda HEAD ko'rsatkichini qayerga ko'chirgan bo'lsangiz (checkout, reset, merge, commit), barchasini ko'rsatadi.

Bash
git reflog
Bu buyruq Git tizimidagi sizning "qutqaruv xaritangiz" hisoblanadi. Undagi har bir satr o'zining qisqa SHA-1 xesh kodiga ega.

7. Reflog SHA dan yangi branch yaratish
Aytaylik, siz reflog ichidan qaysidir yo'qolib ketgan eski holatni topdingiz va joriy branch'ingizni buzmagan holda o'sha holatni alohida ko'rib chiqmoqchisiz.

Bash
# reflog'dan olingan a1b2c3d xesh kodidan yangi 'rescue-branch' ochish
git branch rescue-branch a1b2c3d
Endi bemalol git checkout rescue-branch qilib, o'sha yo'qolgan kodlarni tekshirib olishingiz mumkin.

8. Detached HEAD'dan chiqish
Agar siz branch'ga emas, to'g'ridan-to'g'ri qandaydir commit xeshiga checkout qilsangiz (git checkout a1b2c3d), Git Detached HEAD (ajralgan HEAD) holatiga o'tadi. Bu holatda qilingan commit'lar hech qaysi branch'ga tegishli bo'lmaydi va yo'qolib ketishi mumkin.

Detached HEAD'dan chiqish yo'llari:
O'zgarishlarni saqlab qolgan holda chiqish (Yangi branch ochish):

Bash
git checkout -b yangi-branch-nomi
O'zgarishlarni tashlab yuborib, o'z branch'ingizga qaytish:

Bash
git checkout main
9. git clean -fd kuzatilmayotgan fayllarni o'chirish
Git loyihangizda paydo bo'lgan, lekin hali git add qilinmagan (untracked) ortiqcha fayl va papkalarni kompyuterdan butunlay o'chirib tashlash uchun ishlatiladi.

Bash
# -f (force) - majburiy o'chirish, -d (directory) - papkalarni ham qo'shib o'chirish
git clean -fd
Maslahat: Nimalar o'chib ketishini oldindan ko'rish uchun test rejimini ishlating: git clean -nd

10. Hisobot: Qachon reset, qachon revert?
Mezon	git reset	git revert
Asosiy vazifasi	Tarixni orqaga qaytaradi (commit'larni o'chiradi/ochadi).	Tarixni o'chirmaydi, teskari o'zgarishli yangi commit qo'shadi.
Qayerda ishlatiladi?	Faqat lokal o'zgarishlarda. Serverga push qilinmagan kodlar uchun.	Serverga push qilingan (public) commit'larni xavfsiz bekor qilishda.
Tariqqa ta'siri	Tarixni tozalaydi va chiziqli qiladi (orqaga suradi).	Tarix o'smaydi, aksincha unga yangi revert commit qo'shilgani ko'rinadi.
Xavf darajasi	Yuqori (ayniqsa --hard ishlatilganda).	Judayam past va xavfsiz.
Qisqa qoida:
Agar xatoni qildingiz, lekin hali push qilmadingiz → git reset ishlating.

Agar xatoni qildingiz va u allaqachon serverga ketdi → git revert ishlating.
