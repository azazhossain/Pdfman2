# Pdfman2
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Permanent Source Detector - HSC Management</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600&display=swap');
        body { font-family: 'Hind Siliguri', sans-serif; background: #f4f7f9; margin: 0; padding: 10px; }
        .no-print { text-align: center; padding: 20px; position: sticky; top: 0; background: #f4f7f9; z-index: 1000; }
        .btn { background: #1a73e8; color: white; border: none; padding: 15px 30px; font-size: 18px; border-radius: 8px; cursor: pointer; font-weight: bold; box-shadow: 0 4px 10px rgba(0,0,0,0.1); width: 100%; max-width: 350px; }
        .container { max-width: 850px; margin: auto; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 2px 15px rgba(0,0,0,0.05); }
        .header { text-align: center; border-bottom: 3px solid #1a73e8; margin-bottom: 25px; padding-bottom: 10px; }
        .chapter-box { background: #1a73e8; color: white; padding: 12px; margin-top: 30px; border-radius: 6px; font-size: 1.2em; font-weight: bold; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        td { border: 1px solid #eee; padding: 12px; text-align: left; vertical-align: top; }
        .q { color: #d32f2f; font-weight: 600; margin-bottom: 5px; }
        .a { color: #1b5e20; line-height: 1.6; }
        #status-msg { text-align: center; padding: 20px; font-weight: bold; color: #1a73e8; }
    </style>
</head>
<body>

<div class="no-print">
    <button class="btn" id="dl-btn" onclick="generatePDF()">📥 ৩০০+ প্রশ্নের PDF ডাউনলোড</button>
</div>

<div class="container" id="pdf-area">
    <div class="header">
        <h1 style="margin:0;">ব্যবস্থাপনা ২য় পত্র</h1>
        <p>সরাসরি GitHub API থেকে ডিটেক্ট করা ৩১০টি প্রশ্ন</p>
    </div>

    <div id="status-msg">অপেক্ষা করুন, সরাসরি সোর্স থেকে ৩১০টি প্রশ্ন শনাক্ত করা হচ্ছে...</div>
    <div id="display-area"></div>
</div>

<script>
    // আপনার GitHub ইউজারনেম, রিপোজিটরি এবং ফাইলের পাথ অনুযায়ী API URL
    const GITHUB_API_URL = "https://raw.githubusercontent.com/azazhossain/fcman2nd2.0/main/data.json";

    async function detectDataPermanently() {
        const display = document.getElementById('display-area');
        const status = document.getElementById('status-msg');

        try {
            // সরাসরি GitHub Raw কন্টেন্ট থেকে ডাটা রিড করা
            const response = await fetch(GITHUB_API_URL);
            
            if (!response.ok) throw new Error("API Connection Failed");

            const data = await response.json();
            
            // ডাটা গ্রুপিং এবং ডিটেকশন লজিক
            const grouped = data.reduce((acc, item) => {
                const chapter = item.category || "অন্যান্য";
                if (!acc[chapter]) acc[chapter] = [];
                acc[chapter].push(item);
                return acc;
            }, {});

            let html = "";
            for (let title in grouped) {
                html += `<div class="chapter-box">${title}</div>`;
                html += `<table><tbody>`;
                grouped[title].forEach((item, index) => {
                    html += `<tr>
                        <td style="width:40px; text-align:center; font-weight:bold;">${index + 1}</td>
                        <td>
                            <div class="q">প্রশ্ন: ${item.question}</div>
                            <div class="a">উত্তর: ${item.answer}</div>
                        </td>
                    </tr>`;
                });
                html += `</tbody></table>`;
            }

            display.innerHTML = html;
            status.style.display = 'none'; // সফল হলে স্ট্যাটাস হাইড হবে
        } catch (error) {
            status.innerHTML = `<div style="color:red; border:1px solid red; padding:10px;">
                সরাসরি ডিটেকশন ব্যর্থ! <br> 
                কারণ: সোর্স সাইটের নিরাপত্তা ব্যবস্থা। <br>
                সমাধান: আপনার ফোনের ব্রাউজার ক্যাশ (Cache) ক্লিয়ার করে অথবা Google Chrome ব্রাউজারে Incognito মোডে ট্রাই করুন।
            </div>`;
        }
    }

    function generatePDF() {
        const element = document.getElementById('pdf-area');
        const btn = document.getElementById('dl-btn');
        btn.innerText = "PDF জেনারেট হচ্ছে...";
        btn.disabled = true;

        const opt = {
            margin: 10,
            filename: 'Management_Full_310_Questions.pdf',
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2 },
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' },
            pagebreak: { mode: ['avoid-all', 'css', 'legacy'] }
        };

        html2pdf().set(opt).from(element).save().then(() => {
            btn.innerText = "📥 ৩০০+ প্রশ্নের PDF ডাউনলোড";
            btn.disabled = false;
        });
    }

    // উইন্ডো লোড হওয়ার সাথে সাথে ডিটেকশন শুরু
    window.onload = detectDataPermanently;
</script>

</body>
</html>
