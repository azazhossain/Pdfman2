# Pdfman2
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HSC Management 2.0 - Live PDF</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600&display=swap');
        body { font-family: 'Hind Siliguri', sans-serif; background: #f0f2f5; margin: 0; padding: 15px; }
        .controls { text-align: center; margin: 20px 0; position: sticky; top: 10px; z-index: 1000; }
        .btn { background: #1a73e8; color: white; border: none; padding: 12px 25px; font-size: 16px; border-radius: 8px; cursor: pointer; font-weight: bold; box-shadow: 0 4px 10px rgba(0,0,0,0.2); }
        .container { max-width: 850px; margin: auto; background: white; padding: 25px; border-radius: 12px; }
        .chapter-header { background: #1a73e8; color: white; padding: 10px 15px; margin-top: 30px; border-radius: 5px; font-size: 1.2em; border-left: 6px solid #0d47a1; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        .q { color: #d32f2f; font-weight: bold; margin-bottom: 5px; }
        .a { color: #2e7d32; line-height: 1.5; }
        #status-msg { text-align: center; color: #1a73e8; font-weight: bold; margin-bottom: 15px; }
    </style>
</head>
<body>

<div class="controls">
    <button class="btn" id="dl-btn" onclick="generatePDF()">PDF ডাউনলোড করুন</button>
</div>

<div class="container" id="pdf-area">
    <div style="text-align:center; border-bottom:3px solid #1a73e8; margin-bottom:20px; padding-bottom:10px;">
        <h1 style="margin:0;">ব্যবস্থাপনা ২য় পত্র</h1>
        <p style="margin:5px 0;">৩১০টি প্রশ্ন ও উত্তর - অটোমেটিক ডিটেকশন সিস্টেম</p>
    </div>

    <div id="status-msg">সোর্স ফাইল শনাক্ত করা হচ্ছে...</div>
    <div id="data-output"></div>
</div>

<script>
    const targetJson = "https://azazhossain.github.io/fcman2nd2.0/data.json";

    // এই ফাংশনটি ডাটা শনাক্ত করে টেবিল তৈরি করে
    function renderQuestions(data) {
        const output = document.getElementById('data-output');
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
        document.getElementById('status-msg').innerHTML = "<span style='color:green;'>✓ ৩১০টি প্রশ্ন সফলভাবে শনাক্ত করা হয়েছে!</span>";
    }

    async function startDetection() {
        try {
            // ১. সরাসরি Fetch করার চেষ্টা (যদি একই অরিজিন হয়)
            let response = await fetch(targetJson);
            let data = await response.json();
            renderQuestions(data);
        } catch (e) {
            // ২. ব্যর্থ হলে বিকল্প প্রক্সি দিয়ে শনাক্ত করার চেষ্টা
            try {
                let proxyRes = await fetch(`https://api.allorigins.win/get?url=${encodeURIComponent(targetJson)}`);
                let proxyData = await proxyRes.json();
                renderQuestions(JSON.parse(proxyData.contents));
            } catch (err) {
                // ৩. যদি প্রক্সি কাজ না করে, তবে সরাসরি ব্রাউজার স্টোরেজ বা অন্য উপায়
                document.getElementById('status-msg').innerHTML = "<span style='color:red;'>সরাসরি শনাক্ত করা যাচ্ছে না। দয়া করে VPN চালু করুন অথবা ওয়েবসাইটটি একবার রিফ্রেশ দিন।</span>";
            }
        }
    }

    function generatePDF() {
        const element = document.getElementById('pdf-area');
        const btn = document.getElementById('dl-btn');
        btn.innerText = "প্রসেসিং হচ্ছে...";
        
        const opt = {
            margin: 10,
            filename: 'HSC_Management_2nd.pdf',
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2, useCORS: true },
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' },
            pagebreak: { mode: ['avoid-all', 'css', 'legacy'] }
        };

        html2pdf().set(opt).from(element).save().then(() => {
            btn.innerText = "PDF ডাউনলোড করুন";
        });
    }

    window.onload = startDetection;
</script>
</body>
</html>
