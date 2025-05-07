import { useState, useEffect } from 'react';
import { Button } from "/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "/components/ui/card";
import { Volume2, Check, X, Star, Trophy, Rabbit, Cat, Dog, Home, Award, Zap, Music } from "lucide-react";
import { motion, AnimatePresence } from "framer-motion";

// 30 Animal questions across 3 themes
const ANIMAL_QUIZ_DATA = {
  sounds: [
    // Easy (10)
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-dog-barking-twice-1.mp3", options: ["🐱 Cat", "🐶 Dog", "🐮 Cow", "🦆 Duck"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-cat-meowing-119.mp3", options: ["🦁 Lion", "🐱 Cat", "🐦 Bird", "🐸 Frog"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-rooster-crowing-1210.mp3", options: ["🐔 Chicken", "🦆 Duck", "🦉 Owl", "🦅 Eagle"], answer: 0 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-hen-clucking-1211.mp3", options: ["🐓 Rooster", "🐣 Chick", "🐤 Hen", "🦚 Peacock"], answer: 2 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-sheep-baaing-1744.mp3", options: ["🐐 Goat", "🐑 Sheep", "🦌 Deer", "🐄 Cow"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-cow-mooing-1745.mp3", options: ["🐑 Sheep", "🐮 Cow", "🐐 Goat", "🦌 Deer"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-pig-snorting-1748.mp3", options: ["🐷 Pig", "🐗 Boar", "🦛 Hippo", "🐭 Mouse"], answer: 0 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-horse-neighing-1746.mp3", options: ["🦓 Zebra", "🐴 Horse", "🦄 Unicorn", "🦌 Deer"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-donkey-braying-1747.mp3", options: ["🐴 Horse", "🦓 Zebra", "🐴 Donkey", "🦒 Giraffe"], answer: 2 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-duck-quacking-1749.mp3", options: ["🦆 Duck", "🦢 Swan", "🦅 Eagle", "🦜 Parrot"], answer: 0 },
    
    // Medium (10)
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-monkey-calling-406.mp3", options: ["🐘 Elephant", "🐵 Monkey", "🦍 Gorilla", "🐒 Lemur"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-lion-roar-5.mp3", options: ["🐯 Tiger", "🦁 Lion", "🐻 Bear", "🐺 Wolf"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-elephant-trumpet-1750.mp3", options: ["🦏 Rhino", "🐘 Elephant", "🦛 Hippo", "🦒 Giraffe"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-tiger-growl-1751.mp3", options: ["🦁 Lion", "🐯 Tiger", "🐆 Leopard", "🐺 Wolf"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-wolf-howling-1752.mp3", options: ["🦊 Fox", "🐺 Wolf", "🐕 Dog", "🐈 Cat"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-frog-croaking-1787.mp3", options: ["🐸 Toad", "🐸 Frog", "🦎 Lizard", "🐍 Snake"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-dolphin-screams-1240.mp3", options: ["🐋 Whale", "🐬 Dolphin", "🦭 Seal", "🦈 Shark"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-owl-hooting-1271.mp3", options: ["🦅 Eagle", "🦉 Owl", "🦅 Hawk", "🦜 Parrot"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-bird-whistling-1151.mp3", options: ["🦜 Parrot", "🕊️ Dove", "🐦 Songbird", "🦚 Peacock"], answer: 2 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-bear-growling-1753.mp3", options: ["🐯 Tiger", "🦁 Lion", "🐻 Bear", "🦍 Gorilla"], answer: 2 },
    
    // Hard (10)
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-geese-cackling-1754.mp3", options: ["🦆 Duck", "🦢 Goose", "🦅 Eagle", "🦃 Turkey"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-cricket-chirping-1757.mp3", options: ["🦗 Cricket", "🐜 Ant", "🐝 Bee", "🦟 Mosquito"], answer: 0 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-bees-buzzing-1758.mp3", options: ["🪰 Fly", "🐝 Bee", "🦟 Mosquito", "🦋 Butterfly"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-snake-hissing-1759.mp3", options: ["🦎 Lizard", "🐍 Snake", "🐊 Crocodile", "🦕 Dinosaur"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-seagull-calling-1760.mp3", options: ["🦅 Eagle", "🦆 Duck", "🦢 Seagull", "🦜 Parrot"], answer: 2 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-crow-cawing-1761.mp3", options: ["🦅 Hawk", "🦉 Owl", "🐦 Crow", "🦜 Parrot"], answer: 2 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-woodpecker-pecking-1762.mp3", options: ["🦜 Parrot", "🐦 Woodpecker", "🦅 Eagle", "🦉 Owl"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-pigeon-cooing-1763.mp3", options: ["🕊️ Dove", "🐦 Pigeon", "🦅 Hawk", "🦢 Swan"], answer: 1 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-turkey-gobbling-1764.mp3", options: ["🦃 Turkey", "🐔 Chicken", "🦆 Duck", "🦚 Peacock"], answer: 0 },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-peacock-calling-1765.mp3", options: ["🦚 Peacock", "🦜 Parrot", "🦅 Eagle", "🦢 Swan"], answer: 0 }
  ],
  pictures: [
    // Picture matching questions
    { image: "🦁", options: ["Lion", "Tiger", "Leopard", "Cheetah"], answer: 0 },
    { image: "🦒", options: ["Giraffe", "Deer", "Horse", "Zebra"], answer: 0 },
    { image: "🦊", options: ["Fox", "Wolf", "Coyote", "Jackal"], answer: 0 }
  ],
  spelling: [
    // Spelling challenges
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-dog-barking-twice-1.mp3", hint: "D _ G", answer: "DOG" },
    { sound: "https://assets.mixkit.co/sfx/preview/mixkit-cat-meowing-119.mp3", hint: "C _ T", answer: "CAT" }
  ]
};

// Load progress from localStorage
const loadProgress = () => {
  const saved = localStorage.getItem('animalQuizProgress');
  return saved ? JSON.parse(saved) : {
    completed: [],
    highScores: { sounds: 0, pictures: 0, spelling: 0 },
    unlockedRewards: []
  };
};

export default function UltimateAnimalQuiz() {
  // Game state
  const [mode, setMode] = useState<'menu' | 'sounds' | 'pictures' | 'spelling'>('menu');
  const [difficulty, setDifficulty] = useState<'easy' | 'medium' | 'hard'>('easy');
  const [currentQuestion, setCurrentQuestion] = useState(0);
  const [score, setScore] = useState(0);
  const [streak, setStreak] = useState(0);
  const [progress, setProgress] = useState(loadProgress());
  const [feedback, setFeedback] = useState<{correct: boolean, message: string} | null>(null);
  const [userInput, setUserInput] = useState('');
  const [unlockedReward, setUnlockedReward] = useState<string | null>(null);

  // Audio handling
  const [audio, setAudio] = useState<HTMLAudioElement | null>(null);
  useEffect(() => {
    return () => { audio?.pause() };
  }, [audio]);

  // Get current questions based on mode and difficulty
  const getQuestions = () => {
    if (mode === 'sounds') {
      const start = difficulty === 'easy' ? 0 : difficulty === 'medium' ? 10 : 20;
      return ANIMAL_QUIZ_DATA.sounds.slice(start, start + 10);
    }
    return ANIMAL_QUIZ_DATA[mode];
  };

  const questions = getQuestions();

  // Play sound for current question
  const playSound = () => {
    if (!questions[currentQuestion].sound || audio) return;
    const newAudio = new Audio(questions[currentQuestion].sound);
    newAudio.play();
    setAudio(newAudio);
    newAudio.onended = () => setAudio(null);
  };

  // Handle answer submission
  const handleAnswer = (answerIndex: number | string) => {
    let isCorrect = false;
    
    if (mode === 'sounds' || mode === 'pictures') {
      isCorrect = answerIndex === questions[currentQuestion].answer;
    } else { // Spelling mode
      isCorrect = userInput.toUpperCase() === questions[currentQuestion].answer;
    }

    // Update score and streak
    if (isCorrect) {
      const newStreak = streak + 1;
      setStreak(newStreak);
      let points = 10;
      
      // Streak bonus
      if (newStreak >= 3) {
        points += 10;
        if (!progress.unlockedRewards.includes('streak-master')) {
          unlockReward('streak-master');
        }
      }
      
      setScore(score + points);
      setFeedback({
        correct: true,
        message: ['Great job!', 'Awesome!', 'Perfect!'][Math.floor(Math.random() * 3)]
      });
      
      // Play success sound
      new Audio('https://assets.mixkit.co/sfx/preview/mixkit-correct-answer-tone-2870.mp3').play();
    } else {
      setStreak(0);
      setFeedback({
        correct: false,
        message: ['Try again!', 'Almost there!', 'You can do it!'][Math.floor(Math.random() * 3)]
      });
      new Audio('https://assets.mixkit.co/sfx/preview/mixkit-wrong-answer-fail-notification-946.mp3').play();
    }

    // Move to next question or end quiz
    setTimeout(() => {
      if (currentQuestion < questions.length - 1) {
        setCurrentQuestion(currentQuestion + 1);
        setUserInput('');
        setFeedback(null);
      } else {
        completeQuiz();
      }
    }, 1500);
  };

  // Unlock rewards
  const unlockReward = (reward: string) => {
    const newRewards = [...progress.unlockedRewards, reward];
    setProgress(prev => ({
      ...prev,
      unlockedRewards: newRewards
    }));
    setUnlockedReward(reward);
    setTimeout(() => setUnlockedReward(null), 3000);
    
    // Play reward sound
    new Audio('https://assets.mixkit.co/sfx/preview/mixkit-achievement-bell-600.mp3').play();
  };

  // Complete the quiz
  const completeQuiz = () => {
    const newProgress = {
      ...progress,
      completed: [...progress.completed, `${mode}-${difficulty}`],
      highScores: {
        ...progress.highScores,
        [mode]: Math.max(progress.highScores[mode], score)
      }
    };
    
    // Unlock rewards for high scores
    if (score >= 80 && !progress.unlockedRewards.includes('high-scorer')) {
      unlockReward('high-scorer');
    }
    if (mode === 'sounds' && difficulty === 'hard' && score >= 90 && !progress.unlockedRewards.includes('sound-expert')) {
      unlockReward('sound-expert');
    }
    
    setProgress(newProgress);
    localStorage.setItem('animalQuizProgress', JSON.stringify(newProgress));
  };

  // Reset current quiz
  const resetQuiz = () => {
    setMode('menu');
    setCurrentQuestion(0);
    setScore(0);
    setStreak(0);
    setFeedback(null);
    setUserInput('');
  };

  // Menu Screen
  if (mode === 'menu') {
    return (
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        className="w-full max-w-md mx-auto mt-8"
      >
        <Card>
          <CardHeader>
            <CardTitle className="text-2xl text-center text-purple-600">
              🎮 Animal Adventure Quiz 🎮
            </CardTitle>
            <p className="text-center text-gray-600">
              Choose your game mode:
            </p>
          </CardHeader>
          <CardContent className="space-y-4">
            {/* Sound Quiz Button */}
            <motion.div whileHover={{ scale: 1.03 }} whileTap={{ scale: 0.98 }}>
              <Button
                onClick={() => setMode('sounds')}
                className="w-full h-20 text-lg bg-gradient-to-r from-blue-400 to-purple-400 hover:from-blue-500 hover:to-purple-500"
              >
                <Volume2 className="mr-2" /> Sound Quiz
              </Button>
            </motion.div>
            
            {/* Picture Quiz Button */}
            <motion.div whileHover={{ scale: 1.03 }} whileTap={{ scale: 0.98 }}>
              <Button
                onClick={() => setMode('pictures')}
                className="w-full h-20 text-lg bg-gradient-to-r from-green-400 to-blue-400 hover:from-green-500 hover:to-blue-500"
              >
                <Rabbit className="mr-2" /> Picture Quiz
              </Button>
            </motion.div>
            
            {/* Spelling Quiz Button */}
            <motion.div whileHover={{ scale: 1.03 }} whileTap={{ scale: 0.98 }}>
              <Button
                onClick={() => setMode('spelling')}
                className="w-full h-20 text-lg bg-gradient-to-r from-yellow-400 to-orange-400 hover:from-yellow-500 hover:to-orange-500"
              >
                <Award className="mr-2" /> Spelling Challenge
              </Button>
            </motion.div>
            
            {/* Progress Display */}
            <div className="mt-6 p-4 bg-gray-50 rounded-lg">
              <h3 className="font-semibold mb-2">Your Progress:</h3>
              <div className="grid grid-cols-3 gap-2">
                <div className="bg-blue-50 p-2 rounded text-center">
                  <p className="text-sm">Sound Quiz</p>
                  <p className="font-bold">{progress.highScores.sounds}</p>
                </div>
                <div className="bg-green-50 p-2 rounded text-center">
                  <p className="text-sm">Picture Quiz</p>
                  <p className="font-bold">{progress.highScores.pictures}</p>
                </div>
                <div className="bg-yellow-50 p-2 rounded text-center">
                  <p className="text-sm">Spelling</p>
                  <p className="font-bold">{progress.highScores.spelling}</p>
                </div>
              </div>
            </div>
          </CardContent>
        </Card>
      </motion.div>
    );
  }

  // Difficulty Selection (for sound quiz)
  if (mode === 'sounds' && currentQuestion === 0 && score === 0) {
    return (
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        className="w-full max-w-md mx-auto mt-8"
      >
        <Card>
          <CardHeader>
            <CardTitle className="text-2xl text-center text-purple-600">
              Sound Quiz
            </CardTitle>
            <p className="text-center text-gray-600">
              Choose your difficulty:
            </p>
          </CardHeader>
          <CardContent className="space-y-4">
            <motion.div whileHover={{ scale: 1.03 }} whileTap={{ scale: 0.98 }}>
              <Button
                onClick={() => { setDifficulty('easy'); setCurrentQuestion(1); }}
                className="w-full h-20 text-lg bg-gradient-to-r from-green-400 to-blue-400 hover:from-green-500 hover:to-blue-500"
              >
                <Rabbit className="mr-2" /> Easy (Ages 5-7)
              </Button>
            </motion.div>
            
            <motion.div whileHover={{ scale: 1.03 }} whileTap={{ scale: 0.98 }}>
              <Button
                onClick={() => { setDifficulty('medium'); setCurrentQuestion(1); }}
                className="w-full h-20 text-lg bg-gradient-to-r from-yellow-400 to-orange-400 hover:from-yellow-500 hover:to-orange-500"
              >
                <Cat className="mr-2" /> Medium (Ages 8-10)
              </Button>
            </motion.div>
            
            <motion.div whileHover={{ scale: 1.03 }} whileTap={{ scale: 0.98 }}>
              <Button
                onClick={() => { setDifficulty('hard'); setCurrentQuestion(1); }}
                className="w-full h-20 text-lg bg-gradient-to-r from-red-400 to-pink-400 hover:from-red-500 hover:to-pink-500"
              >
                <Dog className="mr-2" /> Hard (Ages 11-12)
              </Button>
            </motion.div>
            
            <Button 
              onClick={() => setMode('menu')}
              variant="outline"
              className="w-full mt-4"
            >
              <Home className="mr-2 h-4 w-4" /> Back to Menu
            </Button>
          </CardContent>
        </Card>
      </motion.div>
    );
  }

  // Quiz Complete Screen
  if (currentQuestion >= questions.length) {
    return (
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        transition={{ duration: 0.5 }}
      >
        <Card className="w-full max-w-md mx-auto mt-8">
          <CardHeader>
            <CardTitle className="text-2xl text-center text-purple-600">
              Quiz Complete!
            </CardTitle>
          </CardHeader>
          <CardContent className="space-y-6 text-center">
            <motion.div
              animate={{ 
                scale: [1, 1.2, 1],
                rotate: [0, 10, -10, 0]
              }}
              transition={{ 
                duration: 1,
                repeat: Infinity,
                repeatType: "reverse"
              }}
            >
              <Trophy className="w-16 h-16 mx-auto text-yellow-500 fill-yellow-500" />
            </motion.div>
            
            <p className="text-xl">
              Your score: <span className="font-bold">{score}</span> points!
            </p>
            
            {score >= questions.length * 8 && (
              <motion.div
                initial={{ scale: 0 }}
                animate={{ scale: 1 }}
                className="bg-gradient-to-r from-yellow-300 to-yellow-500 p-4 rounded-lg"
              >
                <Star className="w-8 h-8 mx-auto mb-2 fill-white text-white" />
                <p className="font-bold text-white">Quiz Champion! 🎉</p>
              </motion.div>
            )}
            
            <div className="flex justify-center gap-4">
              <Button 
                onClick={resetQuiz}
                className="bg-gradient-to-r from-blue-400 to-purple-400 hover:from-blue-500 hover:to-purple-500"
              >
                Play Again
              </Button>
              <Button 
                onClick={() => {
                  setMode('menu');
                  setCurrentQuestion(0);
                  setScore(0);
                }}
                variant="outline"
              >
                Choose Another Quiz
              </Button>
            </div>
          </CardContent>
        </Card>
      </motion.div>
    );
  }

  // Main Quiz Screen
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.5 }}
    >
      <Card className="w-full max-w-md mx-auto mt-8">
        <CardHeader>
          <CardTitle className="text-2xl text-purple-600">
            {mode === 'sounds' && 'What\'s That Sound? 🎵'}
            {mode === 'pictures' && 'Picture Match! 🖼️'}
            {mode === 'spelling' && 'Spelling Challenge ✏️'}
          </CardTitle>
          <div className="flex items-center gap-2">
            <div className="bg-blue-100 text-blue-800 px-3 py-1 rounded-full">
              Score: {score}
            </div>
            <div className="bg-green-100 text-green-800 px-3 py-1 rounded-full">
              Q: {currentQuestion + 1}/{questions.length}
            </div>
            {streak >= 2 && (
              <div className="bg-yellow-100 text-yellow-800 px-3 py-1 rounded-full">
                Streak: {streak} 🔥
              </div>
            )}
          </div>
        </CardHeader>
        <CardContent className="space-y-6">
          {/* Unlocked Reward Notification */}
          <AnimatePresence>
            {unlockedReward && (
              <motion.div
                initial={{ scale: 0 }}
                animate={{ scale: 1 }}
                exit={{ scale: 0 }}
                className="bg-gradient-to-r from-purple-500 to-pink-500 text-white p-3 rounded-lg text-center"
              >
                <p className="font-bold">
                  {unlockedReward === 'streak-master' && 'Streak Master Unlocked! +10 Bonus 🎁'}
                  {unlockedReward === 'high-scorer' && 'High Scorer Reward! 🏆'}
                  {unlockedReward === 'sound-expert' && 'Sound Expert Achievement! 🎧'}
                </p>
              </motion.div>
            )}
          </AnimatePresence>

          {/* Question Display */}
          <div className="bg-blue-50 p-4 rounded-lg">
            {mode === 'sounds' && (
              <div className="text-center">
                <p className="font-semibold">Listen to the animal sound!</p>
                <Button 
                  onClick={playSound}
                  variant="outline"
                  className="mt-2"
                  disabled={!!audio}
                >
                  <Volume2 className="mr-2 h-4 w-4" />
                  {audio ? "Playing..." : "Play Sound"}
                </Button>
              </div>
            )}
            
            {mode === 'pictures' && (
              <div className="text-center">
                <p className="font-semibold">What animal is this?</p>
                <div className="text-8xl my-4">{questions[currentQuestion].image}</div>
              </div>
            )}
            
            {mode === 'spelling' && (
              <div className="text-center">
                <p className="font-semibold">Spell the animal you hear!</p>
                <Button 
                  onClick={playSound}
                  variant="outline"
                  className="mt-2 mb-4"
                  disabled={!!audio}
                >
                  <Volume2 className="mr-2 h-4 w-4" />
                  {audio ? "Playing..." : "Play Sound"}
                </Button>
                <p className="text-gray-600 mb-2">Hint: {questions[currentQuestion].hint}</p>
                <input
                  type="text"
                  value={userInput}
                  onChange={(e) => setUserInput(e.target.value)}
                  className="border-2 border-gray-300 rounded-lg p-2 w-full text-center uppercase"
                  placeholder="Type the word..."
                />
              </div>
            )}
          </div>

          {/* Answer Options */}
          {(mode === 'sounds' || mode === 'pictures') && (
            <motion.div
              initial={{ y: 20, opacity: 0 }}
              animate={{ y: 0, opacity: 1 }}
              className="grid grid-cols-2 gap-3"
            >
              {questions[currentQuestion].options.map((option, index) => (
                <motion.div
                  key={index}
                  whileHover={{ scale: feedback ? 1 : 1.03 }}
                  whileTap={{ scale: feedback ? 1 : 0.98 }}
                >
                  <Button
                    onClick={() => handleAnswer(index)}
                    variant={
                      !feedback 
                        ? "outline" 
                        : index === questions[currentQuestion].answer 
                          ? "default" 
                          : "outline"
                    }
                    disabled={!!feedback}
                    className="h-20 text-lg w-full"
                  >
                    {option}
                  </Button>
                </motion.div>
              ))}
            </motion.div>
          )}

          {/* Submit Button for Spelling */}
          {mode === 'spelling' && (
            <Button
              onClick={() => handleAnswer(0)}
              disabled={!userInput || !!feedback}
              className="w-full h-12 text-lg bg-gradient-to-r from-green-400 to-blue-400 hover:from-green-500 hover:to-blue-500"
            >
              Submit Answer
            </Button>
          )}

          {/* Feedback */}
          <AnimatePresence>
            {feedback && (
              <motion.div
                initial={{ opacity: 0, y: 20 }}
                animate={{ opacity: 1, y: 0 }}
                className={`p-3 rounded-lg text-center ${
                  feedback.correct
                    ? 'bg-green-100 text-green-800'
                    : 'bg-red-100 text-red-800'
                }`}
              >
                {feedback.correct ? (
                  <>
                    <Check className="inline mr-2" />
                    <span className="font-bold">{feedback.message}</span>
                  </>
                ) : (
                  <>
                    <X className="inline mr-2" />
                    <span className="font-bold">{feedback.message}</span>
                  </>
                )}
              </motion.div>
            )}
          </AnimatePresence>

          {/* Navigation */}
          <Button 
            onClick={() => setMode('menu')}
            variant="outline"
            className="w-full mt-4"
          >
            <Home className="mr-2 h-4 w-4" /> Back to Menu
          </Button>
        </CardContent>
      </Card>
    </motion.div>
  );
}
