<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>เกมภารกิจนักเคมีชุมชนปราณบุรี: แฟกเตอร์เปลี่ยนหน่วย</title>
    <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #007BFF;
            --success: #28A745;
            --danger: #DC3545;
            --background: #E9F4ED; /* สีเขียวอมฟ้า สื่อถึงทะเลและป่าชายเลน */
            --card-bg: #FFFFFF;
        }

        body {
            font-family: 'Kanit', sans-serif;
            background-color: var(--background);
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-image: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
        }

        .game-container {
            background-color: var(--card-bg);
            border-radius: 15px;
            box-shadow: 0 10px 20px rgba(0,0,0,0.1);
            width: 90%;
            max-width: 600px;
            padding: 30px;
            text-align: center;
        }

        h1 {
            color: #0056b3;
            margin-bottom: 5px;
        }

        .subtitle {
            color: #666;
            margin-bottom: 20px;
            font-size: 0.9em;
        }

        .status-bar {
            display: flex;
            justify-content: space-between;
            margin-bottom: 20px;
            font-weight: 600;
            color: #444;
        }

        .question-box {
            background-color: #f8f9fa;
            border-left: 5px solid var(--primary);
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 25px;
            font-size: 1.2em;
        }

        .choices-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        button.choice-btn {
            font-family: 'Kanit', sans-serif;
            background-color: #fff;
            border: 2px solid var(--primary);
            color: var(--primary);
            padding: 15px;
            font-size: 1.1em;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        button.choice-btn:hover {
            background-color: var(--primary);
            color: #fff;
        }

        button.choice-btn:disabled {
            cursor: not-allowed;
            opacity: 0.7;
        }

        .feedback {
            margin-top: 20px;
            padding: 15px;
            border-radius: 8px;
            display: none;
            font-weight: 600;
        }

        .feedback.correct {
            background-color: #d4edda;
            color: var(--success);
            border: 1px solid #c3e6cb;
            display: block;
        }

        .feedback.wrong {
            background-color: #f8d7da;
            color: var(--danger);
            border: 1px solid #f5c6cb;
            display: block;
        }

        .next-btn {
            margin-top: 20px;
            background-color: var(--success);
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 1em;
            border-radius: 5px;
            cursor: pointer;
            display: none;
            font-family: 'Kanit', sans-serif;
        }

        .next-btn:hover {
            background-color: #218838;
        }

        #result-screen {
            display: none;
        }
    </style>
</head>
<body>

<div class="game-container" id="game-screen">
    <h1>ภารกิจนักเคมีชุมชนปราณบุรี</h1>
    <div class="subtitle">ตะลุยโจทย์แฟกเตอร์เปลี่ยนหน่วยในห้องปฏิบัติการมีชีวิต</div>
    
    <div class="status-bar">
        <span id="question-counter">ภารกิจที่: 1 / 4</span>
        <span id="score-display">คะแนน: 0</span>
    </div>

    <div class="question-box" id="question-text">
        กำลังโหลดภารกิจ...
    </div>

    <div class="choices-grid" id="choices-container">
        <!-- ปุ่มคำตอบจะถูกสร้างที่นี่ด้วย JavaScript -->
    </div>

    <div id="feedback-box" class="feedback"></div>
    <button id="next-btn" class="next-btn" onclick="nextQuestion()">ทำภารกิจต่อไป ➔</button>
</div>

<div class="game-container" id="result-screen">
    <h1>สรุปผลภารกิจ</h1>
    <h2 id="final-score">คุณทำได้ 0 / 4 คะแนน</h2>
    <p id="evaluation-text">ยอดเยี่ยม! คุณพร้อมเป็นนักเคมีเพื่อพัฒนาชุมชนปราณบุรีแล้ว</p>
    <button class="choice-btn" style="background-color: var(--primary); color: white; width: 100%; margin-top: 20px;" onclick="location.reload()">เล่นอีกครั้ง</button>
</div>

<script>
    // ฐานข้อมูลคำถาม (อิงบริบทชุมชนปราณบุรี)
    const questions = [
        {
            context: "เก็บตัวอย่างน้ำจาก 'ป่าชายเลนสิรินาถราชินี' เพื่อวัดค่าความเค็ม ปริมาตร 2,500 มิลลิลิตร (mL)",
            question: "คิดเป็นปริมาตรกี่ลิตร (L)?",
            choices: ["0.25 L", "2.5 L", "25 L", "250 L"],
            correctAnswer: 1, // index ของคำตอบที่ถูก
            explanation: "คำนวณโดยใช้แฟกเตอร์: 2,500 mL × (1 L / 1,000 mL) = 2.5 L"
        },
        {
            context: "ทำ 'หมึกแดดเดียวปากน้ำปราณ' ต้องใช้หมึกกล้วยสดน้ำหนัก 4.5 กิโลกรัม (kg)",
            question: "คิดเป็นน้ำหนักกี่กรัม (g)?",
            choices: ["45 g", "450 g", "4,500 g", "45,000 g"],
            correctAnswer: 2,
            explanation: "คำนวณโดยใช้แฟกเตอร์: 4.5 kg × (1,000 g / 1 kg) = 4,500 g"
        },
        {
            context: "วิเคราะห์วิตามินซีใน 'สับปะรดปัตตาเวีย' พบว่ามีวิตามินซีอยู่ 450 มิลลิกรัม (mg)",
            question: "คิดเป็นน้ำหนักกี่กรัม (g)?",
            choices: ["0.045 g", "0.45 g", "4.5 g", "45 g"],
            correctAnswer: 1,
            explanation: "คำนวณโดยใช้แฟกเตอร์: 450 mg × (1 g / 1,000 mg) = 0.45 g"
        },
        {
            context: "ชาวประมงเรือเล็กจับ 'ปูม้า' ได้น้ำหนักรวม 12,000 กรัม (g)",
            question: "คิดเป็นน้ำหนักกี่กิโลกรัม (kg)?",
            choices: ["1.2 kg", "12 kg", "120 kg", "1,200 kg"],
            correctAnswer: 1,
            explanation: "คำนวณโดยใช้แฟกเตอร์: 12,000 g × (1 kg / 1,000 g) = 12 kg"
        }
    ];

    let currentQuestionIndex = 0;
    let score = 0;

    // Elements
    const questionText = document.getElementById('question-text');
    const choicesContainer = document.getElementById('choices-container');
    const feedbackBox = document.getElementById('feedback-box');
    const nextBtn = document.getElementById('next-btn');
    const questionCounter = document.getElementById('question-counter');
    const scoreDisplay = document.getElementById('score-display');

    function loadQuestion() {
        const q = questions[currentQuestionIndex];
        
        // Reset UI
        feedbackBox.className = 'feedback';
        feedbackBox.innerHTML = '';
        nextBtn.style.display = 'none';
        choicesContainer.innerHTML = '';
        
        // Update Status
        questionCounter.innerText = `ภารกิจที่: ${currentQuestionIndex + 1} / ${questions.length}`;
        scoreDisplay.innerText = `คะแนน: ${score}`;

        // Set Question
        questionText.innerHTML = `<strong>สถานการณ์:</strong> ${q.context}<br><br><strong>คำถาม:</strong> ${q.question}`;

        // Set Choices
        q.choices.forEach((choice, index) => {
            const btn = document.createElement('button');
            btn.className = 'choice-btn';
            btn.innerText = choice;
            btn.onclick = () => checkAnswer(index, btn);
            choicesContainer.appendChild(btn);
        });
    }

    function checkAnswer(selectedIndex, btnElement) {
        const q = questions[currentQuestionIndex];
        const buttons = document.querySelectorAll('.choice-btn');
        
        // Disable all buttons after clicking
        buttons.forEach(btn => btn.disabled = true);

        if (selectedIndex === q.correctAnswer) {
            btnElement.style.backgroundColor = 'var(--success)';
            btnElement.style.color = 'white';
            btnElement.style.borderColor = 'var(--success)';
            score++;
            scoreDisplay.innerText = `คะแนน: ${score}`;
            feedbackBox.className = 'feedback correct';
            feedbackBox.innerHTML = `✅ ถูกต้อง! <br> ${q.explanation}`;
        } else {
            btnElement.style.backgroundColor = 'var(--danger)';
            btnElement.style.color = 'white';
            btnElement.style.borderColor = 'var(--danger)';
            
            // Highlight correct answer
            buttons[q.correctAnswer].style.backgroundColor = 'var(--success)';
            buttons[q.correctAnswer].style.color = 'white';
            
            feedbackBox.className = 'feedback wrong';
            feedbackBox.innerHTML = `❌ ยังไม่ถูกต้อง <br> ${q.explanation}`;
        }

        nextBtn.style.display = 'inline-block';
    }

    function nextQuestion() {
        currentQuestionIndex++;
        if (currentQuestionIndex < questions.length) {
            loadQuestion();
        } else {
            showResult();
        }
    }

    function showResult() {
        document.getElementById('game-screen').style.display = 'none';
        document.getElementById('result-screen').style.display = 'block';
        
        document.getElementById('final-score').innerText = `คุณทำได้ ${score} / ${questions.length} คะแนน`;
        
        const evalText = document.getElementById('evaluation-text');
        if(score === questions.length) {
            evalText.innerText = "🏆 สมบูรณ์แบบ! คุณคือนักเคมีตัวจริงของชุมชนปราณบุรี";
        } else if (score >= questions.length / 2) {
            evalText.innerText = "👍 เก่งมาก! ฝึกฝนเรื่องแฟกเตอร์เปลี่ยนหน่วยอีกนิดจะเป๊ะเลย";
        } else {
            evalText.innerText = "💪 พยายามอีกนิดนะ! ลองกลับไปทบทวนเรื่องการตัดหน่วยดูใหม่";
        }
    }

    // เริ่มเกมครั้งแรก
    loadQuestion();
</script>

</body>
</html>
