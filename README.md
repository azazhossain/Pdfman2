# Pdfman2
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HSC Management 2nd Paper - Complete PDF Tool</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600&display=swap');
        body { font-family: 'Hind Siliguri', sans-serif; background: #f8f9fa; margin: 0; padding: 15px; }
        .controls { text-align: center; margin: 20px 0; position: sticky; top: 10px; z-index: 1000; }
        .btn { background: #1a73e8; color: white; border: none; padding: 12px 25px; font-size: 18px; border-radius: 8px; cursor: pointer; font-weight: bold; box-shadow: 0 4px 10px rgba(0,0,0,0.2); }
        .container { max-width: 850px; margin: auto; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 2px 15px rgba(0,0,0,0.1); }
        .chapter-header { background: #1a73e8; color: white; padding: 10px 15px; margin-top: 30px; border-radius: 5px; font-size: 1.2em; border-left: 6px solid #0d47a1; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        .q { color: #d32f2f; font-weight: bold; margin-bottom: 5px; }
        .a { color: #2e7d32; line-height: 1.5; }
        #status-msg { text-align: center; font-weight: bold; color: #1a73e8; margin-bottom: 15px; }
    </style>
</head>
<body>

<div class="controls">
    <button class="btn" id="dl-btn" onclick="generatePDF()">PDF ডাউনলোড করুন</button>
</div>

<div class="container" id="pdf-area">
    <div style="text-align:center; border-bottom:3px solid #1a73e8; margin-bottom:20px; padding-bottom:10px;">
        <h1 style="margin:0;">ব্যবস্থাপনা ২য় পত্র</h1>
        <p style="margin:5px 0;">সম্পূর্ণ ৩১০টি প্রশ্ন ও উত্তর (অধ্যায়ভিত্তিক বিন্যস্ত)</p>
    </div>

    <div id="status-msg">আপনার আপলোড করা ডাটা শনাক্ত করা হচ্ছে...</div>
    <div id="data-output"></div>
</div>

<script>
    async function loadData() {
        const output = document.getElementById('data-output');
        const status = document.getElementById('status-msg');
        
        try {
            // একই রিপোজিটরির data.json ফাইল থেকে ডাটা ডিটেক্ট করছে
            const response = await fetch('data.json');
            if (!response.ok) throw new Error();
            const data = await response.json();

            // অটোমেটিক অধ্যায় শনাক্তকরণ এবং গ্রুপিং
            const grouped = data.reduce((acc, item) => {
                const cat = item.category || "অন্যান্য";
                if (!acc[cat]) acc[cat] = [];
                acc[cat].push(item);
                return acc;
            }, {});

            let html = "";
            for (let chapter in grouped) {
                html += `<div class="chapter-header">${chapter}</div>`;
                html += `<table><thead><tr style="background:#f8f9fa;"><th>নং</th><th>প্রশ্ন ও উত্তর</th></tr></thead><tbody>`;
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
            status.innerHTML = `<span style="color:green;">✓ মোট ${data.length}টি প্রশ্ন শনাক্ত করা হয়েছে!</span>`;

        } catch (error) {
            status.innerHTML = `<span style="color:red;">ত্রুটি: data.json ফাইলটি পাওয়া যায়নি। একই ফোল্ডারে index.html এবং data.json থাকতে হবে।</span>`;
        }
    }

    function generatePDF() {
        const element = document.getElementById('pdf-area');
        const btn = document.getElementById('dl-btn');
        btn.innerText = "অপেক্ষা করুন...";
        
        const opt = {
            margin: 10,
            filename: 'Management_2nd_310_Questions.pdf',
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' },
            pagebreak: { mode: ['avoid-all', 'css', 'legacy'] }
        };

        html2pdf().set(opt).from(element).save().then(() => {
            btn.innerText = "PDF ডাউনলোড করুন";
        });
    }

    window.onload = loadData;
</script>
</body>
</html>
