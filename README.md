# quizgame
import java.util.Scanner;
import java.util.Timer;
import java.util.TimerTask;


class QuizQuestion {
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

    public int getCorrectAnswerIndex() {
        return correctAnswerIndex;
    }
}


public class QuizGame {
    private QuizQuestion[] questions;
    private int score;
    private Scanner scanner;
    private boolean answerSubmitted;
    private int timeLimitPerQuestion;

    public QuizGame(QuizQuestion[] questions, int timeLimitPerQuestion) {
        this.questions = questions;
        this.score = 0;
        this.scanner = new Scanner(System.in);
        this.answerSubmitted = false;
        this.timeLimitPerQuestion = timeLimitPerQuestion;
    }

    public void start() {
        for (QuizQuestion question : questions) {
            presentQuestion(question);
        }
        displayResults();
    }

    private void presentQuestion(QuizQuestion question) {
        System.out.println("\n" + question.getQuestion());
        String[] options = question.getOptions();
        for (int i = 0; i < options.length; i++) {
            System.out.println((i + 1) + ". " + options[i]);
        }

        answerSubmitted = false;
        Timer timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                if (!answerSubmitted) {
                    System.out.println("\nTime's up!");
                    answerSubmitted = true;
                    evaluateAnswer(question, -1); 
                }
            }
        }, timeLimitPerQuestion * 1000);

        int userAnswer = getUserAnswer();
        if (!answerSubmitted) {
            answerSubmitted = true;
            timer.cancel();
            evaluateAnswer(question, userAnswer);
        }
    }

    private int getUserAnswer() {
        System.out.print("Enter your answer (1-4): ");
        while (!scanner.hasNextInt()) {
            System.out.print("Invalid input. Enter your answer (1-4): ");
            scanner.next();
        }
        return scanner.nextInt() - 1;
    }

    private void evaluateAnswer(QuizQuestion question, int userAnswer) {
        if (userAnswer == question.getCorrectAnswerIndex()) {
            System.out.println("Correct!");
            score++;
        } else {
            System.out.println("Incorrect. The correct answer was: " + 
                (question.getCorrectAnswerIndex() + 1) + ". " + 
                question.getOptions()[question.getCorrectAnswerIndex()]);
        }
    }

    private void displayResults() {
        System.out.println("\nQuiz Over!");
        System.out.println("Your final score is: " + score + "/" + questions.length);
        System.out.println("Summary of correct/incorrect answers:");

        for (int i = 0; i < questions.length; i++) {
            QuizQuestion question = questions[i];
            System.out.println((i + 1) + ". " + question.getQuestion());
            System.out.println("Correct answer: " + 
                (question.getCorrectAnswerIndex() + 1) + ". " + 
                question.getOptions()[question.getCorrectAnswerIndex()]);
        }
    }

    public static void main(String[] args) {
        QuizQuestion[] questions = new QuizQuestion[] {
            new QuizQuestion("What is the capital of France?", 
                new String[]{"Berlin", "Madrid", "Paris", "Rome"}, 2),
            new QuizQuestion("What is 2 + 2?", 
                new String[]{"3", "4", "5", "6"}, 1),
            new QuizQuestion("What is the largest planet?", 
                new String[]{"Earth", "Mars", "Jupiter", "Saturn"}, 2),
            new QuizQuestion("What is the chemical symbol for water?", 
                new String[]{"HO", "O2", "H2O", "OH"}, 2)
        };

        QuizGame game = new QuizGame(questions, 10); 
        game.start();
    }
}

