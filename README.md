# git-hub-2
Mana, Git-da yuzaga kelishi mumkin bo'lgan eng qiyin va qo'rqinchli vaziyatlardan xotirjamlik bilan chiqib ketish bo'yicha amaliy va to'liq "Qutqaruv qo'llanmasi".

1. Senariy 1: Branch o'chirish + reflog bilan qaytarish
Nima qildim?
Siz o'z ustingizda ishlagan va hali mainga qo'shilmagan muhim feature-xyz branch'ingizni adashib o'chirib yubordingiz.

Bash
git branch -D feature-xyz
(Git ogohlantirish bergan bo'lsa ham -D kaliti bilan majburlab o'chirib yuborildi).

Nima bo'ldi?
Branch ko'rinmay qoldi, unga checkout qilib bo'lmaydi. Kodlar yo'qolgandek tuyulmoqda.

Qanday qutqardim?
HEAD ko'rsatkichining oxirgi harakatlarini tekshiramiz:

Bash
git reflog
Chiqqan ro'yxatdan o'chib ketgan branch'ning eng oxirgi commit'ini (masalan, moving from feature-xyz to main yozuvidan bitta oldingi qatorni) va uning xesh kodini (masalan, b3c4d5e) topamiz.

O'sha commit turgan joydan branch'ni qayta tiklaymiz:

Bash
git branch feature-xyz b3c4d5e
2. Senariy 2: reset --hard + reflog bilan qaytarish
Nima qildim?
Oxirgi bir nechta commit'larni o'chirish maqsadida noto'g'ri masofani ko'rsatib, hamma narsani tozalab yuboradigan buyruqni berdingiz:

Bash
git reset --hard HEAD~3
Nima bo'ldi?
Oxirgi 3 ta commit va ularning ichidagi barcha yozilgan kodlar ham working directory'dan, ham tarixdan butunlay g'oyib bo'ldi.

Qanday qutqardim?
Git-ning xotira xaritasini ochamiz:

Bash
git reflog
Biz reset buyrug'ini berishimizdan to'g'ri oldingi holatni (ro'yxatda HEAD@{1} yoki o'sha commit xeshini, masalan, a1b2c3d) aniqlaymiz.

Vaqtni orqaga qaytaramiz:

Bash
git reset --hard a1b2c3d
Hamma yo'qolgan kodlar va commit'lar o'z joyiga qaytadi.

3. Senariy 3: Push qilingan yomon commit + revert bilan bekor
Nima qildim?
Lokalda xato kod yozdingiz, commit qildingiz va uni jamoaviy main (yoki develop) branch'ga push qilib yubordingiz:

Bash
git push origin main
Nima bo'ldi?
Xatoli kod hamma jamoadoshlaringizga yetib bordi va serverdagi loyiha buzildi (build fail bo'ldi).

Qanday qutqardim?
Server tarixi ommaviy bo'lgani uchun uni reset bilan o'chirib bo'lmaydi (boshqalarda chalkashlik bo'ladi).

O'sha yomon commit'ning xesh kodini (e5f6g7h) topamiz.

Unga qarama-qarshi bo'lgan yangi tuzatuvchi commit yaratamiz:

Bash
git revert e5f6g7h
Hosil bo'lgan yangi "safe" commit'ni serverga yuboramiz:

Bash
git push origin main
4. Senariy 4: Wrong branch'da commit + reset main'da + branch yaratish
Nima qildim?
Siz yangi vazifa uchun alohida branch ochish esingizdan chiqib, to'g'ridan-to'g'ri main branch'ida kod yozdingiz va 2 ta commit qilib yubordingiz.

Bash
# Hozir main branch'damiz
git commit -m "Yangi feature logikasi 1"
git commit -m "Yangi feature logikasi 2"
Nima bo'ldi?
main branch tarixi buzildi, u yerda hali tugallanmagan xom kodlar paydo bo'lib qoldi.

Qanday qutqardim?
Hozirgi turgan joyimizdan (barcha yangi commit'lar bilan) yangi to'g'ri branch ochib olamiz:

Bash
git checkout -b feature/my-new-task
(Endi bu commit'lar xavfsiz joyda).

Qayta main branch'iga o'tamiz:

Bash
git checkout main
mainni o'sha 2 ta noto'g'ri commit qo'shilishidan oldingi holatiga qaytaramiz (kodlar o'chmaydi, chunki ular yangi branch'da qoldi):

Bash
git reset --hard HEAD~2
5. Senariy 5: Detached HEAD'da ish + branch yaratish reflog'dan
Nima qildim?
Shunchaki eski commit'ni ko'rish uchun git checkout c3b2a1d qildingiz va o'sha yerda turib kod yozib, commit qilib yubordingiz.

Nima bo'ldi?
Siz Detached HEAD (hech qaysi branch'ga birikmagan erkin HEAD) holatida edingiz. Boshqa branch'ga (masalan, git checkout main) o'tganingizdan keyin o'sha yozgan commit'ingiz "havoda" muallaq qolib ketdi va yo'qoldi.

Qanday qutqardim?
Yana qutqaruvchimizni chaqiramiz:

Bash
git reflog
Detached HEAD holatida qilgan commit'ingiz xesh kodini (masalan, f9e8d7c) topasiz.

O'sha havoda qolib ketgan commit'ni o'z ichiga olgan yangi branch ochib, uni qutqarib qolasiz:

Bash
git branch saved-feature f9e8d7c
Bonus Senariy 6: rebase --abort bilan bekor qilish
Nima qildim?
git rebase main buyrug'ini berdingiz, lekin juda ko'p va tushunarsiz konfliktlar (ziddiyatlar) chiqib ketdi. Kod chalkashib ketdi.

Nima bo'ldi?
Rebase jarayoni o'rtasida qolib ketdingiz, nima qilishni bilmayapsiz, lokal fayllar buzilgan holatda turibdi.

Qanday qutqardim?
Hech narsani qo'lda tuzatishga urinib o'tirmasdan, hammasini rebase boshlanishidan oldingi holatga qaytarish buyrug'ini berasiz:

Bash
git rebase --abort
Loyiha xuddi rebase boshlanmagandek avvalgi holatiga qaytadi.

Bonus Senariy 7: git fsck --lost-found ishlatish
Nima qildim?
Siz daxshatli chalkashlik qildingiz, hatto reflog ham tozalangan yoki u yerdan ham kerakli narsani topa olmayapsiz.

Nima bo'ldi?
Git obyektlar bazasida yetim (dangling) qolib ketgan, hech qaysi branch yoki havola ko'rsatmayotgan commit'lar bor, lekin ularning SHA xeshini bilmaysiz.

Qanday qutqardim?
Git-ning ichki fayl tizimini tekshirish (file system check) buyrug'ini bajaramiz:

Bash
git fsck --lost-found
Natija: Git barcha yetim qolgan commit va fayllarni loyihangiz ichidagi .git/lost-found/commit/ papkasiga yig'ib beradi. U yerdagi commit'larni git show <commit_id> qilib ko'rib, ichidagi kodni topib olishingiz mumkin.

Yakuniy Hisobot (Summary Report)
Senariy	Nima qildim?	Nima bo'ldi?	Qanday qutqardim?	Olgan saboqlarim (Takeaways)
1. Branch o'chirish	git branch -D	Branch tarixdan o'chdi	reflog + git branch <nomi> <sha>	Branch bu shunchaki pointer (ko'rsatkich). Commit'lar bazada turadi.
2. Reset --hard	git reset --hard HEAD~3	Commit va kodlar yo'qoldi	reflog dan eski xeshni topib reset --hard	--hard xavfli, lekin lokal harakatlar doim reflogda muhrlanadi.
3. Push yomon commit	git push origin main	Serverdagi loyiha buzildi	git revert <sha> + git push	Server tarixini o'chirma, xatoni teskari o'zgarish bilan tuzat.
4. Wrong branch commit	mainda yangi kod yozildi	main tarixi bulg'andi	Yangi branch ochib, mainni reset --hard qilish	Ish boshlashdan oldin doim git status va to'g'ri branch'dalikni tekshir.
5. Detached HEAD	Erkin holatda commit qilindi	Boshqa branch'ga o'tgach kod yo'qoldi	reflog dan commit SHA topib branch ochish	Detached HEAD rejimida uzoq qolma, darhol branch ochib ol.
6. Rebase chalkashlik	Murakkab rebase konfliktlari	Rebase o'rtasida qolib ketish	git rebase --abort	Agar jarayon nazoratdan chiqsa, qo'rqmasdan abort qilish mumkin.
7. Oxirgi chora (fsck)	Hamma narsa aralashib ketdi	Reflog'dan ham umid uzildi	git fsck --lost-found	Git hech narsani osonlikcha o'chirmaydi, fayl tizimidan ham qidirsa bo'ladi.
Eng katta saboq: Panika qilmaslik!
Git-da ishlashda eng muhim narsa buyruqlarni yodlash emas, balki xotirjamlikni saqlashdir.

Git — bu vaqt mashinasi: Siz terminalda "o'chirish" buyrug'ini berganingizda, Git uni zaxira xotirasiga yashirib qo'yadi. Tizim sizni xatolardan himoya qilish uchun qurilgan.

Qo'rquv xatoni kattalashtiradi: Panikaga tushgan dasturchi internetdan ko'rgan har xil kuchli force buyruqlarini ketma-ket yozib, ahvolni battar qiladi.

Formula oddiy: Muammo bo'ldimi? Klaviaturadan qo'lingizni oling → Chuqur nafas oling → git status va git reflog buyruqlarini yozib, tarixni ko'zdan kechiring. Yechim doim o'sha yerda bo'ladi!
