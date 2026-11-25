# persian-bookshelf

prompt: # Prompt: Leon Vasari, Persian 3D bookshelf (کتاب‌شناسی لئون وازاری)

Build a single-page, scroll-driven 3D bookshelf for a fictional author, Leon Vasari (لئون وازاری). Use one HTML file with plain HTML, CSS and vanilla JavaScript: no framework, no build step, no libraries, and no images (every cover and spine is drawn with inline SVG). The whole page is Persian and right-to-left: `<html lang="fa" dir="rtl">`, Persian digits everywhere (۱۹۷۱), no letter-spacing on Persian text.

## Concept
A warm off-white paper page. Across the middle runs an endless shelf of cloth-bound books standing spine-out. The active book is pulled out and turned in 3D so its front cover faces the viewer: tilted −4.5°, lifted, pushed towards the camera. Scrolling the page moves through the books. Clicking the cover lifts the book off the shelf, opens it on its right-hand spine (Persian binding), and turns into a page-flipping reader.

## Fonts (Google Fonts)
- Markazi Text 400/500/600/700: logo, titles, synopsis, all serif text. Stack: `"Markazi Text","Vazirmatn",serif`.
- Vazirmatn 400/500/600: all labels, tabs, buttons, meta, cursor. Stack: `"Vazirmatn",Tahoma,system-ui,sans-serif`.

## Palette
--paper #EDEBE6 (page) · --sheet #F4F2ED (reader backdrop) · --ink #141414 · --muted #6D6A63 · --cobalt #1F3FB0 (logo, active tab, subtitles, accents) · --rule rgba(20,20,20,.9) · --ease cubic-bezier(.2,.8,.2,1) · --H min(46vh,31vw) desktop, min(58vw,40svh) under 768px (book height; all geometry scales from it).
Paper grain: fixed full-screen layer, z-index 40, pointer-events none, 220px SVG feTurbulence tile (baseFrequency .85, 3 octaves) mapped to warm grey at alpha .22, multiply blend, opacity .55.

## Layout (all position: fixed, centred on the viewport)
1. Top bar, 84px, grid 1fr auto 1fr: «بازگشت» boxed label (start/right); centre logo «لئون وازاری» (Markazi 700, 29px, cobalt) over «کتاب‌شناسی یک نویسنده» (Vazirmatn 500, 10.5px); end/left «سال‌های نوشتن ۱۹۵۸ تا ۱۹۸۹» (hidden on mobile).
2. Decade tabs above the shelf (top: 50% − H/2 − 92px): «همه», «دههٔ ۱۹۵۰», «دههٔ ۱۹۶۰», «دههٔ ۱۹۷۰», «دههٔ ۱۹۸۰». Boxed 28px buttons sharing borders; a cobalt block slides and stretches behind the active one (.6s ease); active text turns white. Active = the decade of the current book. Clicking jumps to that decade's first book.
3. Shelf: full width, top 50% − H/2 − 40px, height H + 80px, perspective 2200px, touch-action pan-y; a zero-size preserve-3d stage at its centre.
4. Caption under the shelf: masked lines that slide (never fade) when the book changes: title (Markazi 600, 38px), «ژانر / کتاب ششم» in cobalt, year in Persian digits.
5. Arrows at caption height, 20px from each side. The left arrow is «کتاب بعدی» (RTL).
6. Footer: «© لئون وازاری. همهٔ حقوق محفوظ است.», a centre hint «برای ورق زدن قفسه اسکرول کنید» (hidden on mobile), and «فیسبوک / توییتر».
7. A small round play-ring button (book icon) on the active cover's lower-left corner; it scales in when the shelf settles.

## Book geometry
ws = H×120/900 (spine width) · d = H×600/900 (cover width = depth) · gap = H×0.022.
Each book is a zero-size preserve-3d element with two faces (backface hidden, each with its own black shade div whose opacity is animated):
- Spine: width ws, translateZ(d/2).
- Cover: width d, `rotateY(-90deg) translateZ(ws/2)` (the left side of the box, so the spine sits to the RIGHT of the front cover, as in Persian books); box-shadow 0 30px 60px −28px rgba(20,16,10,.55).

Render the 12 books three times (36) for an endless look. For relative index r = wrap(v − s) into −18…18:
- x = −F(r), mirrored so the chronology runs right to left. F(r) = r(ws+gap) + (d − ws + 1.6gap)·B(r), where B(r) = (r + sin(πr)/π)/2 for |r|<1, else ±0.5.
- o = smoothstep(max(0, 1−|r|)).
- Transform: `translate3d(x, −o·.025H − lift·.045H, o·.2H) rotateZ(−4.5°·o) rotateX(tiltX·o) rotateY(90°·o + tiltY·o)`.
- z-index = round(100 − |r|·4). Spine shade = o×.55; cover shade = (1−o)×.5.
- Hovered non-active books lift (eased 1−e^(−12dt)). The open cover tilts toward the cursor (±9° Y, ±6° X).
- Write styles only when values change; cull books off-screen.

## Drawn covers (SVG 600×900) and spines (SVG 120×900)
Cover: cloth colour, faint woven-line pattern, inset frame (stroke at 55%), «لئون وازاری» (Markazi 500, 34) at the top, the title split over 1–2 lines (Markazi 700, 92–110), a motif in the art area (y 420–790), «نشر قفسه» (Vazirmatn 500, 20) at the bottom, a dark hinge gradient along the spine edge.
Spine: cloth, two pairs of rules top and bottom, an accent dot near the top, the title rotated 90° (Markazi 700, 50–58), «وازاری» at the base, a cylindrical light gradient.

| # | Title | Lines | Year | Genre | Pages | Cloth / Ink / Accent | Motif |
|---|---|---|---|---|---|---|---|
| 1 | درهٔ خاموش | درهٔ / خاموش | 1958 | رمان | 212 | #E6DCC6 / #1A1917 / #1A1917 | mountains + moon ring |
| 2 | ساعت آبی | ساعت / آبی | 1962 | رمان عاشقانه | 188 | #1F3FB0 / #EFE7D6 / #EFE7D6 | half sun over water lines |
| 3 | رودِ نمک | رودِ / نمک | 1964 | رمان | 264 | #ECE2CD / #1A1917 / #D6402B | red sun, wavy river |
| 4 | آخرین تابستان | آخرین / تابستان | 1966 | رمان | 240 | #1A1917 / #EFE7D6 / #D6402B | flame |
| 5 | جادهٔ سبز | جادهٔ / سبز | 1969 | رمان معمایی | 226 | #1E5A45 / #EFE7D6 / #EFE7D6 | winding road, pines |
| 6 | ظهرِ خاکستر | ظهرِ / خاکستر | 1971 | رمان | 234 | #EEE6D3 / #1F3FB0 / #1F3FB0 | sun ring, horizon, dashes |
| 7 | ناظر | ناظر | 1973 | رمان هیجانی | 198 | #D8C6A2 / #1A1917 / #1A1917 | eye with lashes |
| 8 | پرندگانِ افتاده | پرندگانِ / افتاده | 1975 | رمان | 252 | #EAE3D2 / #1A1917 / #1A1917 | branch, birds in flight |
| 9 | نوری در طوفان | نوری / در طوفان | 1978 | رمان | 286 | #22211F / #EFE7D6 / #D6402B | lighthouse, beams, waves |
| 10 | مدِ بلند | مدِ / بلند | 1981 | داستان‌های کوتاه | 204 | #18308C / #EFE7D6 / #EFE7D6 | moon, narrowing reflection |
| 11 | جنگی آرام | جنگی / آرام | 1985 | رمان طنز | 218 | #2E2B28 / #EFE7D6 / #C9B48A | wall between two houses |
| 12 | درِ سرخ | درِ / سرخ | 1989 | رمان هیجانی | 246 | #8A2920 / #EFE7D6 / #E8654A | red arched door |

Synopses («دربارهٔ کتاب»):
1. معلمی به روستای کوهستانی‌ای برمی‌گردد که پدرش را تبعید کرده بود، و می‌بیند دره هنوز تک‌تک رازهایش را نگه داشته است.
2. در غروبی طولانی در شهری بندری، زنی بیوه و مردی غریبه دروغ‌های کوچک ردوبدل می‌کنند تا نور تمام شود.
3. دو برادر قایقی پر از نمک را در مسیر رود به شهری می‌کشانند که دیگر هیچ‌چیزِ بارشان را نمی‌خواهد.
4. تابستانِ پیش از آنکه سد روستایشان را زیر آب ببرد، سه دوست هرچه را نمی‌توانند با خود ببرند به آتش می‌کشند.
5. نقشه‌برداری که جاده‌ای جنگلی را می‌پیماید، مدام به ردپاهایی برمی‌خورد که همیشه یک روز از او جلوترند.
6. سواری از بیابانی سفید می‌گذرد تا بدهی‌ای قدیمی را تسویه کند، و دیر می‌فهمد چه کسی او را فرستاده است.
7. نگهبانی شبانه کم‌کم گمان می‌کند ساختمانی که از آن محافظت می‌کند، حالا خودش او را زیر نظر گرفته است.
8. وقتی پرنده‌ها بر فراز شهری مرزی دیگر نمی‌خوانند، تنها دخترِ شهردار است که به دنبال دلیلش می‌رود.
9. نگهبان فانوس دریایی و پسرش طوفانی سه‌روزه را کنار ملوانی کشتی‌شکسته سر می‌کنند که نامش را نمی‌گوید.
10. همسر ماهیگیری روی دیوار بندر مدها را می‌شمارد، در حالی که قایق شوهرش به خانه برنمی‌گردد.
11. دو همسایه سی سال بر سر دیوار یک باغ جنگی بی‌صدا به راه می‌اندازند و هیچ‌کدام نمی‌گوید از کجا شروع شد.
12. آپاراتچی‌ای در آخرین شبِ یک سینما، حلقه‌فیلمی ناشناس را در راهروهای آن دنبال می‌کند.

Chapter-one openings («فصل یکم»):
1. برف آن سال زودتر از همیشه نشست. وقتی اتوبوس در پیچ آخر جاده ایستاد، دره همان‌طور بود که پدرش وصفش کرده بود: آرام، سفید، و پر از پنجره‌هایی که پشتشان کسی نگاه می‌کرد.
2. ساعت شش بود و بندر هنوز بوی نمک و قیر می‌داد. زن کنار پنجرهٔ کافه نشسته بود و فنجانش را آن‌قدر چرخاند که مرد غریبه بالاخره پرسید آیا جای کسی را گرفته است.
3. قایق را پیش از سپیده به آب انداختیم. برادرم طناب را دور مچش پیچید و گفت تا شهر سه روز راه است، اگر رود با ما راه بیاید.
4. مهندس‌ها با کلاه‌های سفید آمدند و روی دیوار مدرسه با گچ خطی کشیدند. گفتند آب تا اینجا بالا می‌آید. ما آن شب تصمیم گرفتیم پیش از آب، آتش برسد.
5. ردپا تازه بود؛ هنوز آب در گودی پاشنه‌اش جمع نشده بود. نقشه‌بردار دفترش را باز کرد و تاریخ را نوشت، و بعد دید همین ردپا را دیروز هم، همین‌جا، کشیده است.
6. آفتاب درست بالای سرش ایستاده بود و زیر پای اسب سایه‌ای نمانده بود. نامه را برای بار سوم خواند؛ امضایی نداشت، اما خط را می‌شناخت.
7. شب دوازدهم بود که آسانسور بی‌آنکه کسی دکمه‌ای بزند، به طبقهٔ نهم رفت و برگشت. نگهبان در دفترش نوشت: هیچ اتفاقی نیفتاد. و بعد زیرش خط کشید.
8. صبح آن روز شهر بی‌صدا بیدار شد. دختر شهردار پنجره را باز کرد و منتظر ماند، اما از درخت‌های میدان حتی صدای بال هم نیامد.
9. موج سوم ملوان را روی سنگ‌ها انداخت. پسر چراغ را بالا گرفت و پدرش گفت: نامش را نپرس. دریا هرچه بخواهد بگوید، خودش می‌گوید.
10. او هر روز دو خط روی دیوار بندر می‌کشید، یکی برای مد و یکی برای جزر. روز چهلم گچ تمام شد و قایق هنوز نیامده بود.
11. اولین آجر را آقای صدری گذاشت، یا شاید آقای نیک‌نام؛ هیچ‌کدام یادشان نیست. اما هر دو مطمئن‌اند که دیوار از آن روز یک وجب به سمت دیگری جلو رفته است.
12. آخرین سانس ساعت یازده تمام شد و سالن خالی شد. آپاراتچی حلقه‌ها را می‌شمرد که دید یکی اضافه است؛ روی قوطی‌اش با ماژیک سرخ فقط نوشته بودند: در.

The page opens on book 6, «ظهرِ خاکستر».

## Scrolling and motion
- The page itself scrolls. A track holds 12 snap points (each 0.55 × viewport height, min 320px; the last is a full viewport tall), with `html{scroll-snap-type:y mandatory}`. The scene is position: fixed. T = 12 + scrollY/step.
- One requestAnimationFrame loop. s follows T on a critically damped spring (ω = 9, 24 while dragging, 3 sub-steps a frame), with no overshoot.
- Wheel or trackpad: preventDefault, accumulate delta, and move exactly one book per 40px of delta, with a 380ms lock.
- Horizontal drag on the shelf scrubs (snap off while dragging, nearest book on release); it suppresses the click it ended on.
- Arrows, tabs and keys use smooth scrollTo. ArrowLeft = next, ArrowRight = previous, Enter = open.
- Custom 62px ring cursor (fine pointers only): shrinks over spines, becomes a filled cobalt disc reading «باز کن» over the open cover.
- No opacity fades for text; reveals are mask slides, clips or scales.

## Opening a book
1. A --sheet backdrop grows as a circle clip-path from the cover's centre to the full screen (1100ms, cubic-bezier(.65,0,.35,1)).
2. A large copy of the book lifts off the shelf from the cover's on-screen rect (straightening from −4.5°, 800ms) to the centre of the screen. The real book is hidden meanwhile. Desktop page width = min((vh−190)/1.5, 40vw); mobile = min(86vw, (vh−170)/1.5); height = width × 1.5.
3. The front cover swings open on its right edge to rotateY(179.4°) (1150ms, delay 600ms, cubic-bezier(.45,0,.2,1)). Its back is a solid endpaper in the cloth colour. Two flyleaves follow (178.6° and 178.2°, staggered 110ms), and the spread slides left by half a page so the spine sits at screen centre.
4. The reader chrome rises through masks: logo, «بستن کتاب» (top right), arrows («صفحهٔ بعد» on the left), and a counter «۱ از ۳» with the hint «برای ورق زدن روی صفحه کلیک کنید، اسکرول کنید یا از کلیدهای جهت استفاده کنید».

## The reader (3 spreads; right page is read first)
Pages are #F6F2E8 paper with a gutter shadow toward the spine, stacked page edges via layered box-shadows, text sized in container units (cqh) with sensible px minimums.
- Spread 1: right = dedication «برای آن‌ها / که قفسه‌ها را پر کردند.»; left = title page («لئون وازاری», the title, a cobalt diamond rule, «نشر قفسه / year»).
- Spread 2: right = «شناسنامهٔ کتاب» (عنوان، نویسنده، گونه، چاپ نخست، شمار صفحه، ناشر) with «همهٔ حقوق برای نویسنده محفوظ است.», page ۲; left = «دربارهٔ کتاب» with the synopsis and «کتاب ششم از دوازده کتاب لئون وازاری، نخستین بار در سال … منتشر شد.», page ۳.
- Spread 3: right = a framed colour plate of the cover's motif with «تصویری از «title»», page ۴; left = «فصل یکم», the title, the opening paragraph, then a rule, «پایان بخش نمونه» and a «بستن کتاب» button, page ۵.
- Each page has a running head and a Persian page number on its outer corner.
- Page turns: each sheet rotates on the spine from left to right (1000ms, cubic-bezier(.42,.04,.22,1)); a dark overlay sweeps across the faces as they turn. Unflipped sheets lean 1.8° and 1.0°; flipped ones settle at 177.6° and 177.0°, so the newest page is always on top.
- Turn by clicking the left page (forward) or the right page (back), arrows, wheel (one turn per gesture, 700ms lock), horizontal swipe over 40px, or the arrow keys. The cursor reads «ورق بزن» / «برگرد».
- Mobile shows one page at a time: the spread slides so the current page fills the screen, and the counter reads «صفحهٔ ۲ از ۶».
- Close («بستن کتاب» or Escape): turn read pages back, close flyleaves and cover, shrink the backdrop to the cover, fly the book back into its gap.

## Accessibility and reduced motion
Real buttons and links; the reader is role="dialog" aria-modal="true"; the caption is aria-live="polite"; arrows have Persian aria-labels. With prefers-reduced-motion, every spring and animation completes instantly.

## Acceptance
Opens on «ظهرِ خاکستر», turned out at the centre with spines repeating on both sides. One wheel notch moves exactly one book. Clicking the cover lifts the book, opens it on its right-hand spine and lands on the title page. All three spreads turn smoothly and close back onto the shelf. Every visible word is Persian.
