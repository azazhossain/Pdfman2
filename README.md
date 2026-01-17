# Pdfman2
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HSC Management 2nd Paper PDF Tool</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600&display=swap');
        body { font-family: 'Hind Siliguri', sans-serif; background: #f8f9fa; padding: 15px; margin: 0; }
        .controls { text-align: center; margin: 20px 0; position: sticky; top: 10px; z-index: 1000; }
        .btn { background: #1a73e8; color: white; border: none; padding: 12px 25px; font-size: 16px; border-radius: 6px; cursor: pointer; font-weight: bold; box-shadow: 0 4px 8px rgba(0,0,0,0.1); }
        .container { max-width: 800px; margin: auto; background: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .chapter-header { background: #1a73e8; color: white; padding: 10px; margin-top: 25px; border-radius: 4px; font-size: 1.1em; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #dee2e6; padding: 10px; text-align: left; }
        .q { color: #d32f2f; font-weight: 600; margin-bottom: 4px; }
        .a { color: #2e7d32; }
    </style>
</head>
<body>

<div class="controls">
    <button class="btn" id="dl-btn" onclick="generatePDF()">PDF ডাউনলোড করুন</button>
</div>

<div class="container" id="pdf-area">
    <div style="text-align:center; border-bottom:2px solid #1a73e8; margin-bottom:20px; padding-bottom:10px;">
        <h2 style="margin:0;">ব্যবস্থাপনা ২য় পত্র: সম্পূর্ণ প্রশ্ন উত্তর</h2>
    </div>
    <div id="data-output">ডেটা লোড হচ্ছে...</div>
</div>

<script>
    async function loadLocalData() {
        const output = document.getElementById('data-output');
        try {
            // আপনার নিজের রিপোজিটরি থেকে ডাটা নিচ্ছে
            const response = await fetch('data.json'); 
            const data = await response.json();

            const grouped = data.reduce((acc, item) => {
                const cat = item.category || "সাধারণ অধ্যায়";
                if (!acc[cat]) acc[cat] = [];
                acc[cat].push(item);
                return acc;
            }, {});

            let html = "";
            for (let chapter in grouped) {
                html += `<div class="chapter-header">${chapter}</div>`;
                html += `<table><thead><tr style="background:#f1f3f4;"><th>নং</th><th>প্রশ্ন ও উত্তর</th></tr></thead><tbody>`;
                grouped[chapter].forEach((item, index) => {
                    html += `<tr><td style="width:10%">${index + 1}</td><td><div class="q">প্রশ্ন: ${item.question}</div><div class="a">উত্তর: ${item.answer}</div></td></tr>`;
                });
                html += `</tbody></table>`;
            }
            output.innerHTML = html;
        } catch (e) {
            output.innerHTML = "ত্রুটি: data.json ফাইলটি পাওয়া যায়নি। নিশ্চিত করুন যে আপনি index.html এর পাশে data.json ফাইলটি আপলোড করেছেন।";
        }
    }

    function generatePDF() {
        const element = document.getElementById('pdf-area');
        const btn = document.getElementById('dl-btn');
        btn.innerText = "অপেক্ষা করুন...";
        html2pdf().set({
            margin: 10,
            filename: 'Management_2nd.pdf',
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
        }).from(element).save().then(() => { btn.innerText = "PDF ডাউনলোড করুন"; });
    }

    window.onload = loadLocalData;
</script>
</body>
</html>
