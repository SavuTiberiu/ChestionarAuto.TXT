import javax.swing.*; //Importă clasele necesare pentru crearea interfețelor grafice cu utilizatorul (GUI) folosind Swing.
import java.awt.*; //Importă clasele necesare pentru lucrul cu componente AWT (Abstract Window Toolkit) pentru aranjarea elementelor GUI pe ecran.
import java.awt.event.ActionEvent; //Importă clasele necesare pentru gestionarea evenimentelor în GUI, cum ar fi acțiunile utilizatorului.
import java.awt.event.ActionListener; 
import java.io.BufferedReader; //Importă clasele necesare pentru citirea datelor dintr-un fișier.
import java.io.FileReader;//Importă clasele necesare pentru citirea datelor dintr-un fișier.
import java.io.IOException; //Importă clasa necesară pentru tratarea excepțiilor de tip IO (input/output).
import java.util.*; // Importă clasele de bază ale colecțiilor și alte clase din pachetul java.util.
import java.util.List;
public class Chestionar extends JFrame { //Defineste clasa Chestionar care extinde clasa JFrame pentru a crea fereastra principală a chestionarului.
    private JPanel cardPanel;
    private CardLayout cardLayout;
    private List<ButtonGroup> buttonGroups;
    private List<JRadioButton> selectedOptions;
    private List<String> questions;
    private List<String> correctAnswers;
    private List<String> userAnswers;
    private String selectedOption = "";
    private int correctAnswersCount = 0;
    private int wrongAnswersCount = 0;
    private JLabel correctAnswersLabel;
    private JLabel wrongAnswersLabel;

    public Chestionar() {
        setTitle("Chestionar"); //Setează titlul ferestrei principale a chestionarului.
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); //Setează acțiunea care trebuie efectuată când fereastra este închisă de către utilizator. În acest caz, programul se va încheia.
        setPreferredSize(new Dimension(770, 490)); //Setează dimensiunea preferată a ferestrei principale a chestionarului.

        //Creează un panou (topPanel) care va fi afișat în partea de sus a ferestrei și va conține etichete pentru numărul de răspunsuri corecte și greșite.
        JPanel topPanel = new JPanel();
        correctAnswersLabel = new JLabel("Răspunsuri corecte: 0");
        wrongAnswersLabel = new JLabel("Răspunsuri greșite: 0");
        topPanel.add(correctAnswersLabel);
        topPanel.add(wrongAnswersLabel);

        cardPanel = new JPanel();   //Creează un panou (cardPanel) care va fi utilizat pentru a afișa cardurile cu întrebări în interiorul ferestrei principale. Cardurile vor fi gestionate utilizând CardLayout.
        cardLayout = new CardLayout();  //Creează un obiect CardLayout care va gestiona schimbarea între cardurile cu întrebări în cardPanel.
        cardPanel.setLayout(cardLayout);//Creează un panou (cardPanel) care va fi utilizat pentru a afișa cardurile cu întrebări în interiorul ferestrei principale. Cardurile vor fi gestionate utilizând CardLayout.
        //Inițializează liste și variabilele necesare pentru stocarea întrebărilor, răspunsurilor corecte și răspunsurilor utilizatorului.
        buttonGroups = new ArrayList<>();
        selectedOptions = new ArrayList<>();
        questions = new ArrayList<>();
        correctAnswers = new ArrayList<>();
        userAnswers = new ArrayList<>();
        readQuestionsFromFile("/E:\\Microsoft VS Code\\PROIECTE FACULTATE\\EChestionareAuto\\src\\data.txt"); //Citeste întrebările din fișierul specificat și le adaugă în lista questions.
        addRandomQuestions(); //Adaugă un număr aleator de întrebări selectate în mod aleatoriu în chestionar folosind metoda addRandomQuestions().
 
        //Adaugă panoul topPanel și panoul cardPanel la conținutul ferestrei principale, utilizând un layout de tip BorderLayout.
        getContentPane().add(topPanel, BorderLayout.NORTH);
        getContentPane().add(cardPanel, BorderLayout.CENTER);

        pack(); //Ajustează dimensiunea ferestrei principale pentru a se potrivi conținutului.
        setLocationRelativeTo(null); //Setează poziția ferestrei principale în centrul ecranului.
        setVisible(true); //Face fereastra principală vizibilă pe ecran.
    }
    //Defineste metoda createQuestionPanel() care primește întrebarea, opțiunile și răspunsul corect și creează un panou pentru afișarea întrebării și a opțiunilor asociate.

    private JPanel createQuestionPanel(String question, String option1, String option2, String option3,
                                       String correctAnswer) {
        //Creează un panou (panel) care utilizează un layout de tip GridLayout pentru a aranja componentele într-o singură coloană.
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(5, 1));

        JLabel questionLabel = new JLabel(question); //Creează o etichetă (questionLabel) pentru afișarea întrebării și o adaugă la panou.
        questionLabel.setFont(new Font("Arial", Font.BOLD, 16));
        panel.add(questionLabel);  
        //Creează butoane radio (optionButton1, optionButton2, optionButton3) pentru afișarea opțiunilor și le adaugă la panou.
        JRadioButton optionButton1 = new JRadioButton(option1);
        JRadioButton optionButton2 = new JRadioButton(option2);
        JRadioButton optionButton3 = new JRadioButton(option3);

        ActionListener optionButtonListener = new ActionListener() { //Adaugă un ActionListener la fiecare buton radio pentru a captura selectarea opțiunii.
            @Override
            public void actionPerformed(ActionEvent e) {
                selectedOption = e.getActionCommand().split("\\)")[0].trim();
            }
        };

        optionButton1.addActionListener(optionButtonListener);
        optionButton2.addActionListener(optionButtonListener);
        optionButton3.addActionListener(optionButtonListener);
        //Creează un grup de butoane radio (buttonGroup) și adaugă butoanele radio la acest grup. Astfel, utilizatorul poate selecta o singură opțiune din grup.
        ButtonGroup buttonGroup = new ButtonGroup();
        buttonGroup.add(optionButton1);
        buttonGroup.add(optionButton2);
        buttonGroup.add(optionButton3);
        buttonGroups.add(buttonGroup);

        panel.add(optionButton1);
        panel.add(optionButton2);
        panel.add(optionButton3);
        //Creează un panou (buttonsPanel) care conține butoanele "Răspunde mai târziu", "Șterge răspuns" și "Confirmă răspuns" și le adaugă la panou.
        JPanel buttonsPanel = new JPanel();
        buttonsPanel.setLayout(new FlowLayout());
        //Adaugă acțiuni pentru butoanele "Răspunde mai târziu", "Șterge răspuns" și "Confirmă răspuns". Acțiunile includ schimbarea cardului următor, ștergerea selecției și verificarea răspunsului utilizatorului.
        JButton answerLaterButton = new JButton("Răspunde mai târziu");
        answerLaterButton.setFont(new Font("Arial", Font.PLAIN, 14));
        buttonsPanel.add(answerLaterButton);

        JButton clearAnswerButton = new JButton("Șterge răspuns");
        clearAnswerButton.setFont(new Font("Arial", Font.PLAIN, 14));
        buttonsPanel.add(clearAnswerButton);

        JButton confirmAnswerButton = new JButton("Confirmă răspuns");
        confirmAnswerButton.setFont(new Font("Arial", Font.PLAIN, 14));
        buttonsPanel.add(confirmAnswerButton);

        panel.add(buttonsPanel);

        
        answerLaterButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                cardLayout.next(cardPanel);
                selectedOption = "";
            }
        });

        
        clearAnswerButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                buttonGroup.clearSelection();
                selectedOption = "";
            }
        });

        
        confirmAnswerButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                if (selectedOption.isEmpty()) {
                    JOptionPane.showMessageDialog(Chestionar.this, "Selectează o opțiune.");
                } else {
                    selectedOptions.add(getSelectedRadioButton(buttonGroup));
                    if (selectedOption.equals(correctAnswer)) {
                        JOptionPane.showMessageDialog(Chestionar.this, "Răspuns corect!");
                        correctAnswersCount++;
                        userAnswers.add("Răspunsul tău: " + selectedOption);
                    } else {
                        JOptionPane.showMessageDialog(Chestionar.this, "Răspuns greșit!");
                        wrongAnswersCount++;
                        correctAnswers.add(question + "\nRăspuns corect: " + correctAnswer);
                        userAnswers.add("Răspunsul tău: " + selectedOption);
                    }

                    cardPanel.remove(panel);
                    selectedOption = "";

                    updateAnswerLabels();

                    if (wrongAnswersCount >= 5) {
                        finishQuiz();
                    } else {
                        selectedOption = "";
                    }
                }
            }
        });

        return panel; //Returnează panoul creat.
    }
    //Defineste metoda getSelectedRadioButton() care primește un grup de butoane radio și returnează butonul radio selectat.
    private JRadioButton getSelectedRadioButton(ButtonGroup buttonGroup) {
        for (Enumeration<AbstractButton> buttons = buttonGroup.getElements(); buttons.hasMoreElements(); ) {
            JRadioButton button = (JRadioButton) buttons.nextElement();
            if (button.isSelected()) {
                return button;
            }
        }
        return null;
    }
    //Defineste metoda readQuestionsFromFile() care primește numele fișierului și citeste întrebările din fișier și le adaugă în lista questions.
    private void readQuestionsFromFile(String fileName) {
        try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = br.readLine()) != null) {
                questions.add(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //Defineste metoda selectRandomQuestions() care selectează în mod aleatoriu un număr specific de întrebări din lista questions.
    private List<String> selectRandomQuestions() {
        List<String> selectedQuestions = new ArrayList<>();
        int numQuestions = 26; // Number of random questions to select
        if (numQuestions <= questions.size()) {
            List<Integer> indexes = new ArrayList<>();
            for (int i = 0; i < questions.size(); i++) {
                indexes.add(i);
            }
            for (int i = 0; i < numQuestions; i++) {
                int randomIndex = (int) (Math.random() * indexes.size());
                int questionIndex = indexes.remove(randomIndex);
                selectedQuestions.add(questions.get(questionIndex));
            }
        } else {
            System.out.println("Not enough questions available in the data file.");
        }

        return selectedQuestions;
    }
    // Defineste metoda addRandomQuestions() care adaugă în mod aleatoriu întrebări selectate în chestionar utilizând metodele selectRandomQuestions() și createQuestionPanel().
    private void addRandomQuestions() {
        List<String> selectedQuestions = selectRandomQuestions();
        for (int i = 0; i < selectedQuestions.size(); i++) {
            String question = selectedQuestions.get(i);
            String[] questionParts = question.split("#")[0].split(":");
            if (questionParts.length != 4) {
                System.out.println("Invalid question format: " + question);
                continue;
            }
            String correctAnswer = question.split("#")[1];

            addQuestion(createQuestionPanel("Întrebarea " + (i + 1) + ": " + questionParts[0], questionParts[1],
                    questionParts[2], questionParts[3], correctAnswer), "question" + (i + 1));
        }
    }
    //Defineste metoda addQuestion() care adaugă un panou la cardPanel utilizând un nume asociat.
    private void addQuestion(JPanel panel, String name) {
        cardPanel.add(panel, name);
    }
    //Defineste metoda updateAnswerLabels() care actualizează etichetele pentru numărul de răspunsuri corecte și greșite.

    private void updateAnswerLabels() {
        correctAnswersLabel.setText("Răspunsuri corecte: " + correctAnswersCount);
        wrongAnswersLabel.setText("Răspunsuri greșite: " + wrongAnswersCount);
    }
    //Defineste metoda finishQuiz() care este apelată atunci când utilizatorul a răspuns greșit la cel puțin 5 întrebări. Afișează un mesaj și listează întrebările la care utilizatorul a răspuns greșit împreună cu răspunsurile corecte și răspunsurile utilizatorului.

    private void finishQuiz() {
        JOptionPane.showMessageDialog(Chestionar.this, "Nepromovat.");

        StringBuilder sb = new StringBuilder();
        sb.append("Întrebări la care ai răspuns greșit:\n");

        for (int i = 0; i < correctAnswers.size(); i++) {
            sb.append("\n");
            sb.append(correctAnswers.get(i));
            sb.append("\n");
            sb.append(userAnswers.get(i));
        }

        JOptionPane.showMessageDialog(Chestionar.this, sb.toString());
        System.exit(0);
    }
    //Metoda main() inițializează obiectul Chestionar și îl face vizibil.
    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                new Chestionar();
            }
        });
    }
}
