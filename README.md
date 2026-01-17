# Pdfman2
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HSC Management 2nd Paper Live Extractor</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600&display=swap');
        body { font-family: 'Hind Siliguri', sans-serif; background: #f4f7f6; padding: 15px; }
        .controls { text-align: center; margin-bottom: 20px; position: sticky; top: 10px; z-index: 1000; }
        .btn { background: #1a73e8; color: white; border: none; padding: 12px 25px; font-size: 16px; border-radius: 8px; cursor: pointer; font-weight: bold; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .container { max-width: 850px; margin: auto; background: white; padding: 30px; border-radius: 12px; }
        .chapter-header { background: #1a73e8; color: white; padding: 10px; margin-top: 30px; border-radius: 5px; font-size: 1.2em; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        .q { color: #d32f2f; font-weight: bold; }
        .a { color: #2e7d32; }
        #status { text-align: center; color: #1a73e8; font-weight: bold; margin: 20px; }
    </style>
</head>
<body>

<div class="controls">
    <button class="btn" id="dl-btn" style="display:none;" onclick="generatePDF()">PDF ডাউনলোড করুন</button>
</div>

<div class="container" id="pdf-area">
    <div style="text-align:center; border-bottom:3px solid #1a73e8; padding-bottom:10px; margin-bottom:20px;">
        <h1 style="margin:0;">ব্যবস্থাপনা ২য় পত্র</h1>
        <p>সরাসরি ওয়েবসাইট থেকে ডিটেক্ট করা ৩১০+ প্রশ্ন ও উত্তর</p>
    </div>

    <div id="status">সোর্স ফাইল শনাক্ত করা হচ্ছে...</div>
    <div id="data-output"></div>
</div>

<script>
    // এই লিঙ্কটি একটি ওপেন প্রক্সি হিসেবে কাজ করবে যা সাইট থেকে ডাটা টেনে আনবে
    const dataSource = "https://azazhossain.github.io/fcman2nd2.0/data.json";
    const proxy = "https://api.allorigins.win/get?url=" + encodeURIComponent(dataSource);

    async function detectAndLoad() {
        const output = document.getElementById('data-output');
        const status = document.getElementById('status');
        const dlBtn = document.getElementById('dl-btn');

        try {
            // ১. প্রক্সি সার্ভারের মাধ্যমে ডাটা ডিটেক্ট করা
            const response = await fetch(proxy);
            if (!response.ok) throw new Error("Connection failed");
            
            const result = await response.json();
            const rawData = JSON.parse(result.contents); // মূল ডাটা শনাক্ত করা হলো

            // ২. অধ্যায় বা ক্যাটাগরি অনুযায়ী বিন্যস্ত করা
            const grouped = rawData.reduce((acc, item) => {
                const cat = item.category || "সাধারণ অধ্যায়";
                if (!acc[cat]) acc[cat] = [];
                acc[cat].push(item);
                return acc;
            }, {});

            // ৩. টেবিল তৈরি করা
            let html = "";
            for (let chapter in grouped) {
                html += `<div class="chapter-header">${chapter}</div>`;
                html += `<table><thead><tr style="background:#f8f9fa;"><th>নং</th><th>প্রশ্ন ও উত্তর</th></tr></thead><tbody>`;
                grouped[chapter].forEach((item, index) => {
                    html += `<tr>
                        <td style="width:10%">${index + 1}</td>
                        <td>
                            <div class="q">প্রশ্ন: ${item.question}</div>
                            <div class="a">উত্তর: ${item.answer}</div>
                        </td>
                    </tr>`;
                });
                html += `</tbody></table>`;
            }

            output.innerHTML = html;
            status.style.display = "none";
            dlBtn.style.display = "inline-block";

        } catch (error) {
            status.innerHTML = "ত্রুটি: সোর্স ফাইল ডিটেক্ট করা যায়নি। <br> সোর্স ওয়েবসাইটটি একবার ব্রাউজারে ওপেন করে রিফ্রেশ দিন, তারপর এখানে ট্রাই করুন।";
            console.error(error);
        }
    }

    function generatePDF() {
        const element = document.getElementById('pdf-area');
        const opt = {
            margin: 10,
            filename: 'HSC_Management_Detected.pdf',
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' },
            pagebreak: { mode: ['avoid-all', 'css', 'legacy'] }
        };
        html2pdf().set(opt).from(element).save();
    }

    // অটো ডিটেকশন শুরু
    window.onload = detectAndLoad;
</script>
</body>
</html>
