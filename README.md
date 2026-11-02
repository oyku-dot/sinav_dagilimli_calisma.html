# sinav_dagilimli_calisma.html<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sınav Dağılımlı Edebiyat Çalışma Seti</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 font-sans min-h-screen p-4 flex flex-col items-center">
    <div class="max-w-2xl w-full bg-white rounded-2xl shadow-xl p-6 mt-6">
        <header class="text-center mb-6">
            <h1 class="text-2xl font-extrabold text-indigo-700">Sınav Analizli Edebiyat Çalışma Merkezi</h1>
            <p class="text-gray-500 text-sm mt-1">Hocanın verdiği soru dağılımına özel hazırlanmıştır!</p>
        </header>

        <!-- Sekmeler -->
        <div class="flex justify-center space-x-2 mb-6 border-b pb-4">
            <button onclick="switchTab('quiz')" id="btn-quiz" class="px-4 py-2 bg-indigo-600 text-white font-semibold rounded-lg shadow transition">Sınav Simülasyonu (10 Soru)</button>
            <button onclick="switchTab('list')" id="btn-list" class="px-4 py-2 bg-gray-200 text-gray-700 font-semibold rounded-lg hover:bg-gray-300 transition">Kategorize Arşiv</button>
        </div>

        <!-- Quiz Alanı -->
        <div id="quiz-section">
            <div class="flex justify-between items-center mb-4 text-sm text-gray-600 font-bold">
                <span id="q-counter">Soru 1 / 10</span>
                <span id="q-score" class="text-indigo-600">Puan: 0</span>
            </div>
            <div id="question-category-tag" class="inline-block px-3 py-1 bg-indigo-100 text-indigo-800 text-xs font-semibold rounded-full mb-2">Kategori</div>
            <div id="question-box" class="text-lg font-semibold text-gray-800 mb-4 bg-indigo-50 p-4 rounded-xl border border-indigo-100">
                Soru yükleniyor...
            </div>
            <div id="options-container" class="space-y-3 mb-4"></div>
            <div id="explanation-box" class="hidden p-3 rounded-lg bg-emerald-50 text-emerald-800 text-sm mb-4 border border-emerald-200"></div>
            <button id="next-q-btn" onclick="nextQuestion()" class="w-full bg-indigo-600 text-white py-3 rounded-xl font-bold hover:bg-indigo-700 transition hidden shadow-md">
                Sonraki Soruya Geç
            </button>
        </div>

        <!-- Liste Alanı (Kategorize) -->
        <div id="list-section" class="hidden">
            <h2 class="text-xl font-bold text-gray-800 mb-3">Sınav Konularına Göre Yazar ve Eserler</h2>
            <div class="mb-4">
                <input type="text" id="search-input" onkeyup="filterList()" placeholder="Yazar veya eser ara..." class="w-full p-3 border rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500">
            </div>
            <div id="archive-container" class="space-y-4 max-h-96 overflow-y-auto pr-2">
                <!-- Dinamik liste buraya dolacak -->
            </div>
        </div>

        <!-- Sonuç Ekranı -->
        <div id="result-section" class="hidden text-center py-10">
            <h2 class="text-3xl font-extrabold text-indigo-700 mb-2">Simülasyon Tamamlandı!</h2>
            <p id="final-score-text" class="text-xl text-gray-700 mb-6 font-medium"></p>
            <button onclick="restartApp()" class="bg-indigo-600 text-white px-8 py-3 rounded-xl font-bold hover:bg-indigo-700 transition shadow-lg">
                Tekrar Çöz
            </button>
        </div>
    </div>

    <script>
        const database = [
            // Modernist Hikaye Yazarları (Sınavda 5 soru)
            { author: "Yusuf Atılgan", category: "Modernist Hikaye", works: ["Eylenci", "Bodur Minareden Öte"] },
            { author: "Oğuz Atay", category: "Modernist Hikaye", works: ["Korkuyu Beklerken"] },
            { author: "Vüs'at O. Bener", category: "Modernist Hikaye", works: ["Siyah Beyaz", "Kara Tren", "Mızıkalı Yürüyüş", "Dost", "Yaşamasız", "Kapan"] },
            { author: "Nezihe Meriç", category: "Modernist Hikaye", works: ["Bozbulanık", "Menekşeli Bilinç", "Bir Kara Derin Kuyu", "Topal Koşma", "Gülün İçinde Bülbül Sesi Var", "Dumanaltı"] },
            { author: "Sabahattin Kudret Aksal", category: "Modernist Hikaye", works: ["Yaralı Hayvan", "Gazoz Ağacı"] },
            { author: "Adalet Ağaoğlu", category: "Modernist Hikaye", works: ["Hadi Gidelim", "Yüksek Gerilim", "Sessizliğin İlk Sesi", "Hayatı Savunma Biçimleri"] },
            { author: "Bilge Karasu", category: "Modernist Hikaye", works: ["Troya'da Ölüm Vardı", "Uzun Sürmüş Bir Günün Akşamı", "Göçmüş Kediler Bahçesi", "Kısmet Büfesi"] },
            { author: "Füruzan", category: "Modernist Hikaye", works: ["Benim Sinemalarım", "Parasız Yatılı", "Gül Mevsimidir", "Sevda Dolu Bir Yaz", "Gecenin Öteki Yüzü"] },
            { author: "Rasim Özdenören", category: "Modernist Hikaye", works: ["Hastalar ve Işıklar", "Çözülme", "Gök Sessiz Bir Ölüm", "Ansızın Yola Çıkmak", "Denize Açılan Bir Kapı"] },
            { author: "Sevinç Çokum", category: "Modernist Hikaye", works: ["Eğik Ağaçlar", "Derin Yara", "Rozalya Ana", "Onlardan Kalan", "Makine", "Bölüşmek", "Gece Kuşu Uzun Öter", "Bir Eski Sokak Sesi", "Evlerinin Önü"] },
            { author: "Leyla Erbil", category: "Modernist Hikaye", works: ["Gecede", "Hallaç", "Eski Sevgili"] },
            { author: "Ferit Edgü", category: "Modernist Hikaye", works: ["Doğu Öyküleri", "Do Sesi", "Nijinski Öyküleri", "Bozgun", "Kaçkın", "Bir Gemide", "Çığlık"] },
            { author: "Haldun Taner", category: "Modernist Hikaye", works: ["Tuş", "Ay Işığında Çalışkur", "Konçinalar", "Yaşasın Demokrasi", "Şişhane'ye Yağmur Yağıyordu", "Sanço'nun Sabah Yürüyüşü"] },
            { author: "Tomris Uyar", category: "Modernist Hikaye", works: ["İpek ve Bakır", "Gece Gezen Kızlar", "Yürekde Bukağı", "Yaz Düşleri ve Salıverilen Hikayesi"] },

            // Bireyin İç Dünyasını Esas Alan Hikayeler (Sınavda 3 soru)
            { author: "Tarık Buğra", category: "Bireyin İç Dünyasını Esas Alan Hikaye", works: ["Oğlumuz", "İki Uyku Arasında", "Yarın Diye Bir Şey Yoktur"] },
            { author: "Peyami Safa", category: "Bireyin İç Dünyasını Esas Alan Hikaye", works: ["İstanbul Hikayeleri", "Beyaz Gölgeler", "Sümbüllerin Gölgesinde", "Ateş Böcekleri", "Bir Mekteplinin Hatırası: Karanlıklar Kralı"] },
            { author: "Oktay Akbal", category: "Bireyin İç Dünyasını Esas Alan Hikaye", works: ["Önce Ekmekler Bozuldu", "Bizans Definesi", "Aşksız İnsanlar", "Aşka Geç Kaldım", "Berber Aynası", "Yalnızlık Bana Yasak"] },
            { author: "Sait Faik Abasıyanık", category: "Bireyin İç Dünyasını Esas Alan Hikaye", works: ["Semaver", "Şahmerdan", "Sarnıç", "Mahalle Kahvesi", "Az Şekerli", "Son Kuşlar", "Alemdağ'da Var Bir Yılan", "Havada Bulut", "Kumpanya", "Tüneldeki Çocuk"] },
            { author: "Kenan Hulusi Koray", category: "Bireyin İç Dünyasını Esas Alan Hikaye", works: ["Bir Yudum Su", "Bir Otelde 7 Kişi", "Son Öpüş", "Bahar Hikayeleri"] },
            { author: "Mustafa Kutlu", category: "Bireyin İç Dünyasını Esas Alan Hikaye", works: ["Ortadaki Adam", "Gönül İşi", "Uzun Hikaye", "Bu Böyledir", "Beyhude Ömrüm", "Mavi Kuş", "Yoksulluk İçimizde", "Yokuşa Akan Sular", "Ya Tahammül Ya Sefer"] },

            // Toplumcu Gerçekçi Hikayeler (Sınavda 1 soru)
            { author: "Sabahattin Ali", category: "Toplumcu Gerçekçi Hikaye", works: ["Ses", "Sırça Köşk", "Kağnı", "Yeni Dünya", "Değirmen"] },
            { author: "Sadri Ertem", category: "Toplumcu Gerçekçi Hikaye", works: ["Bay Virgül", "Bacayı İndir Bacayı Kaldır", "Silindir Şapka Giyen Köylü", "Korku"] },
            { author: "Orhan Kemal", category: "Toplumcu Gerçekçi Hikaye", works: ["Ekmek Kavgası", "Kardeş Payı", "Önce Ekmek", "72. Koğuş", "Dünya'da Harp Vardı", "Çamaşırcının Kızı", "İşsiz", "Sarmaşık", "Grev"] },
            { author: "Fakir Baykurt", category: "Toplumcu Gerçekçi Hikaye", works: ["Çilli", "Efendilik Savaşı", "Karın Ağrısı", "Can Parası", "İçerdeki Oğul", "Anadolu Garajı", "Onbinlerce Kağnı", "Cüce Muhammet"] },

            // Millî ve Dînî Duyarlılığı Yansıtan Hikaye (Sınavda 1 soru)
            { author: "Halide Edip Adıvar", category: "Millî ve Dînî Duyarlılık Hikaye", works: ["Dağa Çıkan Kurt", "İzmir'den Bursa'ya", "Harap Mabetler", "Kubbede Kalan Hoş Bir Seda"] },
            { author: "Yakup Kadri Karaosmanoğlu", category: "Millî ve Dînî Duyarlılık Hikaye", works: ["Milli Savaş Hikayeleri", "Bir Serencam", "Rahmet"] },
            { author: "Refik Halit Karay", category: "Millî ve Dînî Duyarlılık Hikaye", works: ["Memleket Hikayeleri", "Gurbet Hikayeleri", "Şeftali Bahçeleri", "Testi, Zincir, Eskici"] },

            // Saf Şiir (Şiir sınavında 5 soru)
            { author: "Ahmet Hamdi Tanpınar", category: "Saf Şiir", works: ["Şiirler (Bursa'da Zaman)"] },
            { author: "Necip Fazıl Kısakürek", category: "Saf Şiir", works: ["Örümcek Ağı", "Çile", "Kaldırımlar", "Ben ve Ötesi", "Sonsuzluk Kervanı"] },
            { author: "Ahmet Muhip Dıranas", category: "Saf Şiir", works: ["Şiirler (Fahriye Abla, Kar, Olvido, Ağrı, Serenad)"] },
            { author: "Cahit Sıtkı Tarancı", category: "Saf Şiir", works: ["35 Yaş", "Ömrümde Sükût", "Sonrası", "Düşten Güzel"] },
            { author: "Asaf Halet Çelebi", category: "Saf Şiir", works: ["OM Mani Padme Hum", "He", "Lâm Alif"] },

            // Toplumcu Şiir (Şiir sınavında 3 soru)
            { author: "Nâzım Hikmet Ran", category: "Toplumcu Şiir", works: ["Güneşi İçenlerin Türküsü", "835 Satır", "Taranta Babu'ya Mektuplar"] },
            { author: "Rıfat Ilgaz", category: "Toplumcu Şiir", works: ["Yarenlik", "Sınıf"] },
            { author: "Ahmet Arif", category: "Toplumcu Şiir", works: ["Hasretinden Prangalar Eskittim"] },
            { author: "Can Yücel", category: "Toplumcu Şiir", works: ["Sevgi Duvarı", "Şiir Alayı"] },

            // Millî ve Dînî Duyarlılık Şiir (Şiir sınavında 2 soru)
            { author: "Arif Nihat Asya", category: "Millî ve Dînî Duyarlılık Şiir", works: ["Bir Bayrak Rüzgar Bekliyor", "Dualar ve Aminler", "Fatihler Ölmez"] },
            { author: "Kemalettin Kamu", category: "Millî ve Dînî Duyarlılık Şiir", works: ["Gurbet", "Bingöl Çobanları"] },
            { author: "Orhan Şaik Gökyay", category: "Millî ve Dînî Duyarlılık Şiir", works: ["Bu Vatan Kimin"] }
        ];

        let currentIdx = 0;
        let score = 0;
        let answered = false;
        let quizPool = [];

        function switchTab(tab) {
            const quizSec = document.getElementById("quiz-section");
            const listSec = document.getElementById("list-section");
            const btnQuiz = document.getElementById("btn-quiz");
            const btnList = document.getElementById("btn-list");
            const resultSec = document.getElementById("result-section");

            if (tab === 'quiz') {
                quizSec.classList.remove("hidden");
                listSec.classList.add("hidden");
                resultSec.classList.add("hidden");
                btnQuiz.className = "px-4 py-2 bg-indigo-600 text-white font-semibold rounded-lg shadow transition";
                btnList.className = "px-4 py-2 bg-gray-200 text-gray-700 font-semibold rounded-lg hover:bg-gray-300 transition";
            } else {
                quizSec.classList.add("hidden");
                resultSec.classList.add("hidden");
                listSec.classList.remove("hidden");
                btnList.className = "px-4 py-2 bg-indigo-600 text-white font-semibold rounded-lg shadow transition";
                btnQuiz.className = "px-4 py-2 bg-gray-200 text-gray-700 font-semibold rounded-lg hover:bg-gray-300 transition";
                renderArchive();
            }
        }

        function initQuiz() {
            quizPool = [...database].sort(() => 0.5 - Math.random()).slice(0, 10);
            currentIdx = 0;
            score = 0;
            document.getElementById("result-section").classList.add("hidden");
            document.getElementById("quiz-section").classList.remove("hidden");
            loadQuestion();
        }

        function loadQuestion() {
            if (currentIdx >= quizPool.length) {
                document.getElementById("quiz-section").classList.add("hidden");
                document.getElementById("result-section").classList.remove("hidden");
                document.getElementById("final-score-text").innerText = `Toplam Puanın: ${score} / ${quizPool.length * 10}`;
                return;
            }

            answered = false;
            const currentObj = quizPool[currentIdx];
            document.getElementById("q-counter").innerText = `Soru ${currentIdx + 1} / ${quizPool.length}`;
            document.getElementById("q-score").innerText = `Puan: ${score}`;
            document.getElementById("question-category-tag").innerText = currentObj.category;
            
            const randomWork = currentObj.works[Math.floor(Math.random() * currentObj.works.length)];
            document.getElementById("question-box").innerText = `'${randomWork}' adlı eser yukarıdaki kategorilerden hangisine giren hangi yazara aittir?`;

            let options = [currentObj.author];
            while (options.length < 4) {
                let randomAuthor = database[Math.floor(Math.random() * database.length)].author;
                if (!options.includes(randomAuthor)) {
                    options.push(randomAuthor);
                }
            }
            options.sort(() => 0.5 - Math.random());

            const container = document.getElementById("options-container");
            container.innerHTML = "";
            options.forEach(opt => {
                const btn = document.createElement("button");
                btn.className = "w-full text-left p-3 rounded-xl border border-gray-200 hover:bg-indigo-50 hover:border-indigo-300 transition font-medium text-gray-700 shadow-sm";
                btn.innerText = opt;
                btn.onclick = () => checkAnswer(opt, currentObj.author);
                container.appendChild(btn);
            });

            document.getElementById("explanation-box").classList.add("hidden");
            document.getElementById("next-q-btn").classList.add("hidden");
        }

        function checkAnswer(selected, correct) {
            if (answered) return;
            answered = true;

            const buttons = document.getElementById("options-container").children;
            for (let b of buttons) {
                if (b.innerText === correct) {
                    b.classList.add("bg-emerald-100", "border-emerald-500", "text-emerald-800");
                }
                if (b.innerText === selected && selected !== correct) {
                    b.classList.add("bg-rose-100", "border-rose-500", "text-rose-800");
                }
            }

            if (selected === correct) {
                score += 10;
                document.getElementById("q-score").innerText = `Puan: ${score}`;
            }

            const expBox = document.getElementById("explanation-box");
            const authorData = database.find(d => d.author === correct);
            expBox.innerText = `Doğru Cevap: ${correct} (${authorData ? authorData.category : ''})`;
            expBox.classList.remove("hidden");
            document.getElementById("next-q-btn").classList.remove("hidden");
        }

        function nextQuestion() {
            currentIdx++;
            loadQuestion();
        }

        function restartApp() {
            initQuiz();
        }

        function renderArchive() {
            const container = document.getElementById("archive-container");
            container.innerHTML = "";
            database.forEach(item => {
                const div = document.createElement("div");
                div.className = "p-4 border rounded-xl bg-white shadow-sm";
                div.innerHTML = `<h3 class="font-bold text-indigo-700">${item.author} <span class="text-xs bg-indigo-50 text-indigo-600 px-2 py-0.5 rounded ml-2">${item.category}</span></h3>
                                 <p class="text-sm text-gray-600 mt-1"><b>Eserler:</b> ${item.works.join(", ")}</p>`;
                container.appendChild(div);
            });
        }

        function filterList() {
            let query = document.getElementById("search-input").value.toLowerCase();
            const container = document.getElementById("archive-container");
            container.innerHTML = "";
            
            database.filter(item => item.author.toLowerCase().includes(query) || item.category.toLowerCase().includes(query) || item.works.some(w => w.toLowerCase().includes(query))).forEach(item => {
                const div = document.createElement("div");
                div.className = "p-4 border rounded-xl bg-white shadow-sm";
                div.innerHTML = `<h3 class="font-bold text-indigo-700">${item.author} <span class="text-xs bg-indigo-50 text-indigo-600 px-2 py-0.5 rounded ml-2">${item.category}</span></h3>
                                 <p class="text-sm text-gray-600 mt-1"><b>Eserler:</b> ${item.works.join(", ")}</p>`;
                container.appendChild(div);
            });
        }

        window.onload = function() {
            initQuiz();
        };
    </script>
</body>
</html>
