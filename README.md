# JTask4
import java.util.Scanner;
import java.util.Timer;
import java.util.TimerTask;

public class QuizApplication {
    private static class QuizQuestion {
        private String question;
        private String[] options;
        private int correctAnswerIndex;

        public QuizQuestion(String question, String[] options, int correctAnswerIndex) {
            this.question = question;
            this.options = options;
            this.correctAnswerIndex = correctAnswerIndex;
        }

        public String getQuestion() {
            return question;
        }

        public String[] getOptions() {
            return options;
        }

        public boolean isCorrect(int userAnswerIndex) {
            return userAnswerIndex == correctAnswerIndex;
        }
    }

    private static class QuizTimer {
        private Timer timer;
        private int secondsLeft;
        private boolean timeUp;

        public QuizTimer(int seconds) {
            this.secondsLeft = seconds;
            this.timeUp = false;
        }

        public void start() {
            timer = new Timer();
            timer.scheduleAtFixedRate(new TimerTask() {
                @Override
                public void run() {
                    if (secondsLeft > 0) {
                        secondsLeft--;
                    } else {
                        timeUp = true;
                        timer.cancel();
                    }
                }
            }, 1000, 1000);
        }

        public int getSecondsLeft() {
            return secondsLeft;
        }

        public boolean isTimeUp() {
            return timeUp;
        }

        public void stop() {
            timer.cancel();
        }
    }

    private static QuizQuestion[] questions = {
        new QuizQuestion("What is the capital of France?", 
                         new String[]{"London", "Paris", "Berlin", "Madrid"}, 1),
        new QuizQuestion("Who wrote 'Hamlet'?", 
                         new String[]{"William Shakespeare", "Charles Dickens", "Leo Tolstoy", "Jane Austen"}, 0),
        new QuizQuestion("Which planet is known as the Red Planet?", 
                         new String[]{"Venus", "Jupiter", "Mars", "Saturn"}, 2)
    };

    private static int score = 0;
    private static int currentQuestionIndex = 0;
    private static QuizTimer timer = new QuizTimer(10); // 10 seconds per question
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        System.out.println("Welcome to the Quiz!");
        System.out.println("You will have 10 seconds to answer each question.");
        System.out.println("Enter the number corresponding to your answer.");
        System.out.println("Let's begin!\n");

        while (currentQuestionIndex < questions.length) {
            displayQuestion();
            timer.start();
            waitForAnswer();
            timer.stop();

            if (timer.isTimeUp()) {
                System.out.println("\nTime's up! Moving to the next question.\n");
                currentQuestionIndex++;
                continue;
            } else {
                System.out.println();
            }

            currentQuestionIndex++;
        }

        displayResult();
        scanner.close();
    }

    private static void displayQuestion() {
        QuizQuestion question = questions[currentQuestionIndex];
        System.out.println("Question " + (currentQuestionIndex + 1) + ": " + question.getQuestion());

        String[] options = question.getOptions();
        for (int i = 0; i < options.length; i++) {
            System.out.println((i + 1) + ". " + options[i]);
        }
    }

    private static void waitForAnswer() {
        System.out.print("\nEnter your answer: ");
        String input = scanner.nextLine().trim();

        if (input.isEmpty()) {
            System.out.println("No answer provided. Moving to the next question.");
            return;
        }

        try {
            int userAnswerIndex = Integer.parseInt(input) - 1;

            if (userAnswerIndex >= 0 && userAnswerIndex < questions[currentQuestionIndex].getOptions().length) {
                if (questions[currentQuestionIndex].isCorrect(userAnswerIndex)) {
                    System.out.println("Correct!");
                    score++;
                } else {
                    System.out.println("Incorrect!");
                }
            } else {
                System.out.println("Invalid answer. Skipping to the next question.");
            }
        } catch (NumberFormatException e) {
            System.out.println("Invalid input. Please enter a number.");
        }

        while (!timer.isTimeUp()) {
            try {
                Thread.sleep(100); // Wait for 0.1 seconds
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private static void displayResult() {
        System.out.println("\nQuiz completed!");
        System.out.println("Your score: " + score + "/" + questions.length);

        System.out.println("\nSummary of answers:");
        for (int i = 0; i < questions.length; i++) {
            QuizQuestion question = questions[i];
            String result = question.isCorrect(i) ? "Correct" : "Incorrect";
            System.out.println("Question " + (i + 1) + ": " + result);
        }
    }
}
