import React, { useState, useEffect } from 'react';

// Define the trivia questions directly within the component for simplicity.
// In a larger application, these might come from an API or a separate file.
const allQuestions = [
  {
    question: 'What is the capital of France?',
    options: ['London', 'Paris', 'Berlin', 'Madrid'],
    answer: 1 // Index of the correct answer (Paris)
  },
  {
    question: 'Which planet is known as the Red Planet?',
    options: ['Mars', 'Venus', 'Jupiter', 'Saturn'],
    answer: 0 // Index of the correct answer (Mars)
  },
  {
    question: 'What is the largest ocean on Earth?',
    options: ['Atlantic Ocean', 'Indian Ocean', 'Arctic Ocean', 'Pacific Ocean'],
    answer: 3 // Index of the correct answer (Pacific Ocean)
  },
  {
    question: 'Who painted the Mona Lisa?',
    options: ['Vincent van Gogh', 'Pablo Picasso', 'Leonardo da Vinci', 'Claude Monet'],
    answer: 2 // Index of the correct answer (Leonardo da Vinci)
  },
  {
    question: 'What is the chemical symbol for water?',
    options: ['O2', 'H2O', 'CO2', 'NaCl'],
    answer: 1 // Index of the correct answer (H2O)
  },
  {
    question: 'What is the smallest country in the world?',
    options: ['Monaco', 'Vatican City', 'Nauru', 'San Marino'],
    answer: 1 // Index of the correct answer (Vatican City)
  },
  {
    question: 'How many continents are there?',
    options: ['5', '6', '7', '8'],
    answer: 2 // Index of the correct answer (7)
  },
  {
    question: 'What is the highest mountain in the world?',
    options: ['Mount Everest', 'K2', 'Kangchenjunga', 'Lhotse'],
    answer: 0 // Index of the correct answer (Mount Everest)
  },
  {
    question: 'Which country is famous for the Great Wall?',
    options: ['India', 'Japan', 'China', 'Egypt'],
    answer: 2 // Index of the correct answer (China)
  },
  {
    question: 'What is the currency of Japan?',
    options: ['Yuan', 'Won', 'Dollar', 'Yen'],
    answer: 3 // Index of the correct answer (Yen)
  }
];

// Helper function to shuffle an array
const shuffleArray = (array) => {
  let currentIndex = array.length, randomIndex;
  // While there remain elements to shuffle.
  while (currentIndex !== 0) {
    // Pick a remaining element.
    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex--;
    // And swap it with the current element.
    [array[currentIndex], array[randomIndex]] = [
      array[randomIndex], array[currentIndex]];
  }
  return array;
};

// Main App component
function App() {
  const [questions, setQuestions] = useState([]);
  const [currentQuestionIndex, setCurrentQuestionIndex] = useState(0);
  const [score, setScore] = useState(0);
  const [showScore, setShowScore] = useState(false);
  const [feedback, setFeedback] = useState('');
  const [selectedAnswer, setSelectedAnswer] = useState(null);
  const [canProceed, setCanProceed] = useState(false);

  // Initialize game on component mount or restart
  const startGame = () => {
    // Shuffle all questions and take a subset if needed, or all of them
    const shuffledQuestions = shuffleArray([...allQuestions]);
    setQuestions(shuffledQuestions);
    setCurrentQuestionIndex(0);
    setScore(0);
    setShowScore(false);
    setFeedback('');
    setSelectedAnswer(null);
    setCanProceed(false);
  };

  useEffect(() => {
    startGame();
  }, []); // Run only once on initial mount

  // Handle user selecting an answer
  const handleAnswerOptionClick = (optionIndex) => {
    if (selectedAnswer === null) { // Allow selection only if no answer has been picked yet
      setSelectedAnswer(optionIndex);
      setCanProceed(true); // Allow user to proceed after selecting an answer

      const isCorrect = optionIndex === questions[currentQuestionIndex].answer;
      if (isCorrect) {
        setScore((prevScore) => prevScore + 1);
        setFeedback('Correct!');
      } else {
        setFeedback(`Incorrect! The correct answer was: ${questions[currentQuestionIndex].options[questions[currentQuestionIndex].answer]}`);
      }
    }
  };

  // Handle progressing to the next question or finishing the game
  const handleNextQuestion = () => {
    const nextQuestion = currentQuestionIndex + 1;
    if (nextQuestion < questions.length) {
      setCurrentQuestionIndex(nextQuestion);
      setFeedback(''); // Clear feedback for the next question
      setSelectedAnswer(null); // Reset selected answer
      setCanProceed(false); // Reset canProceed for the next question
    } else {
      setShowScore(true); // End of the game, show score
    }
  };

  // Render the current question
  const renderQuestion = () => {
    if (!questions.length) return null; // Handle case where questions are not loaded yet
    const currentQuestion = questions[currentQuestionIndex];
    return (
      <div className="flex flex-col space-y-4 w-full">
        <div className="text-xl md:text-2xl font-bold text-gray-800 text-center mb-6">
          <span className="text-blue-600">Question {currentQuestionIndex + 1}</span>/{questions.length}
        </div>
        <div className="text-2xl md:text-3xl font-semibold text-center text-gray-900 mb-8">
          {currentQuestion.question}
        </div>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 w-full">
          {currentQuestion.options.map((option, index) => (
            <button
              key={index}
              onClick={() => handleAnswerOptionClick(index)}
              // Apply dynamic styling based on selection and correctness
              className={`
                w-full p-4 rounded-xl shadow-lg border-2
                transition-all duration-300 ease-in-out
                ${selectedAnswer === null
                  ? 'bg-blue-100 text-blue-800 border-blue-300 hover:bg-blue-200 hover:border-blue-400'
                  : index === currentQuestion.answer
                    ? 'bg-green-100 text-green-800 border-green-400 scale-105' // Correct answer
                    : index === selectedAnswer && index !== currentQuestion.answer
                      ? 'bg-red-100 text-red-800 border-red-400 scale-105' // Incorrect selected answer
                      : 'bg-gray-100 text-gray-600 border-gray-300 cursor-not-allowed opacity-70' // Unselected after one is picked
                }
                ${selectedAnswer !== null ? 'cursor-not-allowed' : 'cursor-pointer'}
                ${selectedAnswer !== null && index === currentQuestion.answer ? 'animate-pulse' : ''}
                font-medium text-lg md:text-xl
              `}
              disabled={selectedAnswer !== null} // Disable buttons after an answer is selected
            >
              {option}
            </button>
          ))}
        </div>
        {feedback && (
          <div className={`mt-6 p-3 rounded-lg text-center font-bold text-lg md:text-xl
            ${feedback.includes('Correct') ? 'bg-green-500 text-white' : 'bg-red-500 text-white'}
            shadow-md`}>
            {feedback}
          </div>
        )}
        {canProceed && (
          <button
            onClick={handleNextQuestion}
            className="mt-8 px-8 py-4 bg-gradient-to-r from-purple-600 to-indigo-700
                       text-white font-bold text-xl rounded-xl shadow-xl
                       hover:from-purple-700 hover:to-indigo-800 transition-all
                       duration-300 ease-in-out transform hover:scale-105
                       focus:outline-none focus:ring-4 focus:ring-purple-300"
          >
            {currentQuestionIndex === questions.length - 1 ? 'Finish Game' : 'Next Question'}
          </button>
        )}
      </div>
    );
  };

  // Render the final score screen
  const renderScoreScreen = () => {
    return (
      <div className="flex flex-col items-center justify-center p-8 bg-white rounded-2xl shadow-2xl space-y-6 animate-fade-in">
        <h2 className="text-4xl font-extrabold text-purple-700 text-center">
          Game Over!
        </h2>
        <p className="text-3xl font-semibold text-gray-800 text-center">
          You scored <span className="text-green-600 text-5xl">{score}</span> out of <span className="text-blue-600 text-5xl">{questions.length}</span> questions.
        </p>
        <button
          onClick={startGame}
          className="mt-6 px-10 py-5 bg-gradient-to-r from-green-500 to-teal-600
                     text-white font-bold text-2xl rounded-full shadow-lg
                     hover:from-green-600 hover:to-teal-700 transition-all
                     duration-300 ease-in-out transform hover:scale-105
                     focus:outline-none focus:ring-4 focus:ring-green-300"
        >
          Play Again
        </button>
      </div>
    );
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-100 via-purple-100 to-pink-100
                    flex items-center justify-center p-4 sm:p-6 md:p-8 font-inter">
      <div className="w-full max-w-2xl bg-white rounded-3xl shadow-2xl p-6 sm:p-8 md:p-10
                      flex flex-col items-center justify-center min-h-[500px] border border-gray-200">
        <h1 className="text-4xl md:text-5xl font-extrabold text-center text-gray-900 mb-8 sm:mb-10 animate-slide-down">
          🧠 Awesome Trivia Quiz 🧠
        </h1>
        {showScore ? renderScoreScreen() : renderQuestion()}
      </div>
    </div>
  );
}

export default App;


