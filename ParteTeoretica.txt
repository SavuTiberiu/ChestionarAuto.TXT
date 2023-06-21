import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class ParteaTeoretica extends JFrame {
    public ParteaTeoretica(boolean isAdmin) {
        JFrame frame = new JFrame("Partea teoretica");
        frame.setSize(1250, 750);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);
        JPanel panel = new JPanel();
        JTextArea textArea = new JTextArea();
        textArea.setEditable(false);
        // Textul cu legislația codului rutier
        String legislatieCodRutier = "Reguli generale de circulație: Acestea includ reguli referitoare la viteza de deplasare, acordarea priorității, semnalizare, depășiri, circulația în sens giratoriu, trecerea la culoarea roșie a semaforului etc.\n"
                +
                "Semne de circulație: Legislația stabilește semnele și marcajele rutiere utilizate pentru a ghida și a avertiza participanții la trafic cu privire la condiții și reguli specifice.\n"
                +
                "Conducerea sub influența alcoolului sau drogurilor: Legea interzice conducerea unui vehicul sub influența alcoolului, substanțelor stupefiante sau medicamentelor care pot afecta capacitatea de a conduce în siguranță.\n"
                +
                "Protecția pietonilor și bicicliștilor: Legislația codului rutier acordă o atenție specială siguranței pietonilor și bicicliștilor, stabilind reguli privind trecerea de pietoni, utilizarea pistelor de biciclete și drepturile acestora în trafic.\n"
                +
                "Siguranța vehiculelor: Legea stabilește cerințe tehnice și de siguranță pentru vehicule, cum ar fi verificarea tehnică periodică, utilizarea centurii de siguranță, funcționarea corectă a sistemelor de iluminare și semnalizare etc.\n"
                +
                "Asigurarea obligatorie a autovehiculelor (RCA): Legea impune proprietarilor de vehicule să încheie o asigurare de răspundere civilă auto (RCA) pentru a acoperi eventualele daune cauzate terților în urma unui accident.\n";
        textArea.setText(legislatieCodRutier);
        textArea.setBounds(50, 50, 300, 200);
        panel.add(textArea);
        JButton printButton = new JButton("Listare la imprimantă");
        printButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {      
            }
        });
        if (isAdmin) {
            JButton adminButton = new JButton("Gestionare utilizatori");
            adminButton.addActionListener(new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                    
                }
            });
        }
        frame.add(panel);
    }
}