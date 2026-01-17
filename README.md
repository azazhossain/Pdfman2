# Pdfman2
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HSC Management 2.0 Dynamic Extractor</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600&display=swap');
        body { font-family: 'Hind Siliguri', sans-serif; background: #f0f4f8; padding: 15px; }
        .controls { text-align: center; margin-bottom: 20px; position: sticky; top: 10px; z-index: 1000; }
        .btn { background: #1a73e8; color: white; border: none; padding: 15px 30px; font-size: 18px; border-radius: 8px; cursor: pointer; font-weight: bold; }
        .container { max-width: 900px; margin: auto; background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .chapter-header { background: #1a73e8; color: white; padding: 12px; margin-top: 30px; border-radius: 5px; font-size: 1.3em; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        .q { color: #d32f2f; font-weight: bold; }
        .a { color: #2e7d32; }
        #status { text-align: center; color: #1a73e8; font-weight: bold; padding: 20px; }
    </style>
</head>
<body>

<div class="controls">
    <button class="btn" id="dl-btn" style="display:none;" onclick="downloadPDF()">PDF ডাউনলোড করুন (৩১০টি প্রশ্ন)</button>
</div>

<div class="container" id="printable">
    <div style="text-align:center; border-bottom:3px solid #1a73e8; padding-bottom:15px; margin-bottom:20px;">
        <h1 style="margin:0;">ব্যবস্থাপনা ২য় পত্র</h1>
        <p>সরাসরি সোর্স থেকে ডিটেক্ট করা পূর্ণাঙ্গ ৩১০টি প্রশ্ন ও উত্তর</p>
    </div>

    <div id="status">সিস্টেম ৩১০টি প্রশ্ন ডিটেক্ট করছে... দয়া করে ৫-১০ সেকেন্ড অপেক্ষা করুন।</div>
    <div id="output"></div>
</div>

<script>
    // এই ফাংশনটি সরাসরি ওই সাইটের ৩১০টি তথ্য ডিটেক্ট করবে
    async function detectAllQuestions() {
        const output = document.getElementById('output');
        const status = document.getElementById('status');
        const dlBtn = document.getElementById('dl-btn');

        try {
            // প্রক্সি ব্যবহার করে সরাসরি ডাটা ডিটেক্ট করা
            const response = await fetch('https://api.allorigins.win/get?url=' + encodeURIComponent('https://azazhossain.github.io/fcman2nd2.0/data.json'));
            const json = await response.json();
            const allData = JSON.parse(json.contents);

            // অধ্যায় অনুযায়ী সাজানো (Grouping)
            const grouped = allData.reduce((acc, item) => {
                const cat = item.category || "সাধারণ প্রশ্ন";
                if (!acc[cat]) acc[cat] = [];
                acc[cat].push(item);
                return acc;
            }, {});

            let html = "";
            for (let chapter in grouped) {
                html += `<div class="chapter-header">${chapter}</div>`;
                html += `<table><thead><tr style="background:#f1f3f4;"><th>নং</th><th>প্রশ্ন ও উত্তর</th></tr></thead><tbody>`;
                
                grouped[chapter].forEach((item, index) => {
                    html += `<tr>
                        <td style="width:10%; text-align:center;">${index + 1}</td>
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
            status.innerHTML = "ত্রুটি: সোর্স থেকে ডাটা ডিটেক্ট করা যাচ্ছে না। আপনার ইন্টারনেট চেক করুন অথবা পেজটি রিফ্রেশ দিন।";
        }
    }

    function downloadPDF() {
        const element = document.getElementById('printable');
        const btn = document.getElementById('dl-btn');
        btn.innerText = "PDF জেনারেট হচ্ছে...";
        
        const opt = {
            margin: 10,
            filename: 'Management_2nd_Full_310.pdf',
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' },
            pagebreak: { mode: ['avoid-all', 'css', 'legacy'] }
        };
        
        html2pdf().set(opt).from(element).save().then(() => {
            btn.innerText = "PDF ডাউনলোড করুন (৩১০টি প্রশ্ন)";
        });
    }

    window.onload = detectAllQuestions;
</script>

</body>
</html>
