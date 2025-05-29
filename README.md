<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kuis Bahasa Korea</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom font for a better look */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f4f8; /* Light blue-gray background */
        }
        /* Custom styles for better aesthetics */
        .quiz-container {
            background-color: #ffffff;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
        }
        .quiz-button {
            transition: all 0.2s ease-in-out;
        }
        .quiz-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }
        .correct-feedback {
            color: #10B981; /* Green-500 */
        }
        .incorrect-feedback {
            color: #EF4444; /* Red-500 */
        }
        .korean-word {
            font-size: 2.5rem; /* Larger font for Korean words */
            font-weight: 700; /* Bold */
            color: #2D3748; /* Darker text for emphasis */
        }
        .input-field {
            border: 2px solid #D1D5DB; /* Light gray border */
            padding: 0.75rem 1rem;
            border-radius: 0.5rem; /* Rounded corners */
            font-size: 1.125rem;
            color: #4B5563;
        }
        .input-field:focus {
            outline: none;
            border-color: #3B82F6; /* Blue-500 on focus */
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2); /* Blue focus ring */
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4">
    <div class="quiz-container max-w-lg w-full p-8 rounded-xl space-y-6 text-center">
        <h1 class="text-4xl font-bold text-gray-800 mb-4">Kuis Bahasa Korea</h1>
        <p class="text-lg text-gray-600 mb-6">Tebak arti kata atau frasa Korea berikut dalam Bahasa Indonesia!</p>

        <div id="quiz-area" class="space-y-6">
            <div class="text-2xl font-semibold text-gray-700">
                Pertanyaan <span id="question-number">1</span> dari <span id="total-questions">30</span>
            </div>
            <div class="korean-word mb-4" id="korean-word"></div>
            
            <input type="text" id="answer-input" class="input-field w-full" placeholder="Ketik jawaban Anda di sini...">
            
            <div id="feedback" class="text-lg font-medium mt-4 min-h-[2rem]"></div>
            
            <button id="check-button" class="quiz-button bg-blue-600 text-white px-6 py-3 rounded-lg font-semibold shadow-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-75 w-full md:w-auto">
                Cek Jawaban
            </button>
            <button id="next-button" class="quiz-button bg-green-600 text-white px-6 py-3 rounded-lg font-semibold shadow-md hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-opacity-75 w-full md:w-auto hidden">
                Pertanyaan Selanjutnya
            </button>
        </div>

        <div id="result-area" class="space-y-4 hidden">
            <h2 class="text-3xl font-bold text-gray-800">Kuis Selesai!</h2>
            <p class="text-xl text-gray-700">Skor Anda: <span id="final-score" class="font-bold"></span> dari <span id="max-score" class="font-bold"></span></p>
            <button id="restart-button" class="quiz-button bg-purple-600 text-white px-6 py-3 rounded-lg font-semibold shadow-md hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-opacity-75 w-full md:w-auto">
                Mulai Ulang Kuis
            </button>
        </div>

        <p class="text-sm text-gray-500 mt-8">Dibuat oleh Andilatif</p>
    </div>

    <script>
        // Array of Korean words and their Indonesian translations
        const quizData = [
            { korean: "안녕하세요", indonesian: "Halo" },
            { korean: "감사합니다", indonesian: "Terima kasih" },
            { korean: "네", indonesian: "Ya" },
            { korean: "아니요", indonesian: "Tidak" },
            { korean: "죄송합니다", indonesian: "Maaf" },
            { korean: "실례합니다", indonesian: "Permisi" },
            { korean: "안녕히 계세요", indonesian: "Selamat tinggal (yang pergi)" },
            { korean: "안녕히 가세요", indonesian: "Selamat tinggal (yang tinggal)" },
            { korean: "물", indonesian: "Air" },
            { korean: "밥", indonesian: "Nasi" },
            { korean: "먹다", indonesian: "Makan" },
            { korean: "마시다", indonesian: "Minum" },
            { korean: "저", indonesian: "Saya" },
            { korean: "당신", indonesian: "Anda" },
            { korean: "이름", indonesian: "Nama" },
            { korean: "무엇", indonesian: "Apa" },
            { korean: "언제", indonesian: "Kapan" },
            { korean: "어디", indonesian: "Di mana" },
            { korean: "왜", indonesian: "Mengapa" },
            { korean: "어떻게", indonesian: "Bagaimana" },
            { korean: "하나", indonesian: "Satu" },
            { korean: "둘", indonesian: "Dua" },
            { korean: "셋", indonesian: "Tiga" },
            { korean: "넷", indonesian: "Empat" },
            { korean: "다섯", indonesian: "Lima" },
            { korean: "여섯", indonesian: "Enam" },
            { korean: "일곱", indonesian: "Tujuh" },
            { korean: "여덟", indonesian: "Delapan" },
            { korean: "아홉", indonesian: "Sembilan" },
            { korean: "열", indonesian: "Sepuluh" }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let shuffledQuizData = [];

        // Get DOM elements
        const koreanWordElement = document.getElementById('korean-word');
        const answerInput = document.getElementById('answer-input');
        const feedbackElement = document.getElementById('feedback');
        const checkButton = document.getElementById('check-button');
        const nextButton = document.getElementById('next-button');
        const restartButton = document.getElementById('restart-button');
        const quizArea = document.getElementById('quiz-area');
        const resultArea = document.getElementById('result-area');
        const questionNumberElement = document.getElementById('question-number');
        const totalQuestionsElement = document.getElementById('total-questions');
        const finalScoreElement = document.getElementById('final-score');
        const maxScoreElement = document.getElementById('max-score');

        // Function to shuffle the quiz data
        function shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]]; // Swap elements
            }
            return array;
        }

        // Function to start or restart the quiz
        function startQuiz() {
            shuffledQuizData = shuffleArray([...quizData]); // Shuffle a copy of the data
            currentQuestionIndex = 0;
            score = 0;
            totalQuestionsElement.textContent = shuffledQuizData.length;
            maxScoreElement.textContent = shuffledQuizData.length;

            quizArea.classList.remove('hidden');
            resultArea.classList.add('hidden');
            nextButton.classList.add('hidden');
            checkButton.classList.remove('hidden');
            answerInput.disabled = false;
            answerInput.value = '';
            feedbackElement.textContent = '';

            loadQuestion();
        }

        // Function to load a new question
        function loadQuestion() {
            if (currentQuestionIndex < shuffledQuizData.length) {
                const currentQuestion = shuffledQuizData[currentQuestionIndex];
                koreanWordElement.textContent = currentQuestion.korean;
                answerInput.value = ''; // Clear previous answer
                feedbackElement.textContent = ''; // Clear feedback
                checkButton.classList.remove('hidden');
                nextButton.classList.add('hidden');
                answerInput.disabled = false;
                answerInput.focus(); // Focus on the input field
                questionNumberElement.textContent = currentQuestionIndex + 1;
            } else {
                endQuiz();
            }
        }

        // Function to check the user's answer
        function checkAnswer() {
            const userAnswer = answerInput.value.trim().toLowerCase();
            const correctAnswer = shuffledQuizData[currentQuestionIndex].indonesian.toLowerCase();

            if (userAnswer === correctAnswer) {
                feedbackElement.textContent = 'Benar!';
                feedbackElement.classList.remove('incorrect-feedback');
                feedbackElement.classList.add('correct-feedback');
                score++;
            } else {
                feedbackElement.textContent = `Salah. Jawaban yang benar adalah: ${shuffledQuizData[currentQuestionIndex].indonesian}`;
                feedbackElement.classList.remove('correct-feedback');
                feedbackElement.classList.add('incorrect-feedback');
            }
            answerInput.disabled = true; // Disable input after checking
            checkButton.classList.add('hidden');
            nextButton.classList.remove('hidden');
        }

        // Function to move to the next question
        function nextQuestion() {
            currentQuestionIndex++;
            loadQuestion();
        }

        // Function to end the quiz and display results
        function endQuiz() {
            quizArea.classList.add('hidden');
            resultArea.classList.remove('hidden');
            finalScoreElement.textContent = score;
        }

        // Event Listeners
        checkButton.addEventListener('click', checkAnswer);
        nextButton.addEventListener('click', nextQuestion);
        restartButton.addEventListener('click', startQuiz);

        // Allow pressing Enter to check answer
        answerInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter' && !answerInput.disabled) {
                checkAnswer();
            } else if (event.key === 'Enter' && answerInput.disabled) {
                nextQuestion();
            }
        });

        // Initialize the quiz when the page loads
        window.onload = startQuiz;
    </script>
</body>
</html>
