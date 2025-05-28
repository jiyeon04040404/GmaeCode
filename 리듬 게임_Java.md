//랜덤으로 동그라미들이 생겨나서 1초 뒤에 사라짐.
//Q = 파란색
//W = 빨간색
//E = 초록색
//R = 노란색
//색깔에 맞게 할당되어 있는 키를 누르면 동그라미가 사라지며 점수 +1

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.*;

public class J_Team_Pj extends JPanel implements KeyListener {
    private int score = 0;
    private javax.swing.Timer gameTimer;  // javax.swing.Timer로 수정
    private ArrayList<Note> notes;
    private long gameStartTime;
    private final int GAME_TIME = 30000; // 게임 진행 시간 (30초)

    // 노트 종류 (Q, W, E, R에 각 색상 매핑)
    private final String[] keys = {"Q", "W", "E", "R"};
    private final Color[] colors = {Color.BLUE, Color.RED, Color.GREEN, Color.YELLOW};

    public J_Team_Pj() {
        notes = new ArrayList<>();
        setFocusable(true);
        addKeyListener(this);

        // gameTimer 초기화: 게임 루프 및 업데이트 주기 설정 (10ms마다 갱신)
        gameTimer = new javax.swing.Timer(10, e -> updateGame());
        gameTimer.start();  // 타이머 시작
        gameStartTime = System.currentTimeMillis();
    }

    // 노트 객체 정의
    private class Note {
        String key;
        int xPosition;
        int yPosition;
        long spawnTime;
        Color color;

        public Note(String key, Color color) {
            this.key = key;
            this.color = color;
            this.xPosition = new Random().nextInt(getWidth() - 50);  // 화면 크기에 맞는 랜덤 x위치
            this.yPosition = new Random().nextInt(getHeight() - 50);  // 화면 크기에 맞는 랜덤 y위치
            this.spawnTime = System.currentTimeMillis();
        }

        public void move() {
            // 동그라미 이동은 없지만, 이곳을 나중에 확장 가능
        }
    }

    // 게임 업데이트
    private void updateGame() {
        long currentTime = System.currentTimeMillis();
        if (currentTime - gameStartTime >= GAME_TIME) {
            gameTimer.stop();  // 게임 종료
            JOptionPane.showMessageDialog(this, "게임 종료! 최종 점수: " + score, "게임 종료", JOptionPane.INFORMATION_MESSAGE);
            System.exit(0);
        }

        // 노트 생성 (랜덤한 확률로)
        if (Math.random() < 0.05) {
            String randomKey = keys[new Random().nextInt(keys.length)];
            Color randomColor = colors[new Random().nextInt(colors.length)];
            notes.add(new Note(randomKey, randomColor));
        }

        // 노트 1초 지나면 삭제
        Iterator<Note> iterator = notes.iterator();
        while (iterator.hasNext()) {
            Note note = iterator.next();
            if (System.currentTimeMillis() - note.spawnTime >= 1000) {
                iterator.remove();  // 1초 지나면 노트 삭제
            }
        }

        repaint();  // 화면 갱신
    }

    @Override
    public void paintComponent(Graphics g) {
        super.paintComponent(g);

        // 배경 그리기
        g.setColor(Color.BLACK);
        g.fillRect(0, 0, getWidth(), getHeight());

        // 노트 그리기
        for (Note note : notes) {
            g.setColor(note.color);
            g.fillOval(note.xPosition, note.yPosition, 50, 50);  // 동그라미 크기 50x50
        }

        // 점수 출력
        g.setColor(Color.WHITE);
        g.setFont(new Font("Arial", Font.BOLD, 20));
        g.drawString("점수: " + score, 20, 30);
    }

    @Override
    public void keyTyped(KeyEvent e) {}

    @Override
    public void keyPressed(KeyEvent e) {
        String pressedKey = KeyEvent.getKeyText(e.getKeyCode());

        // 키가 일치하는 노트를 찾기
        Iterator<Note> iterator = notes.iterator();
        while (iterator.hasNext()) {
            Note note = iterator.next();
            if (note.key.equals(pressedKey)) {
                long currentTime = System.currentTimeMillis();
                if (currentTime - note.spawnTime <= 1000) {  // 1초 이내에 맞춘 경우
                    score += 1;  // 점수 +1
                    iterator.remove();  // 맞춘 노트 삭제
                }
                break;
            }
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {}

    // 게임 시작 메소드
    public static void main(String[] args) {
        JFrame frame = new JFrame("리듬 게임");
        J_Team_Pj gamePanel = new J_Team_Pj();
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(500, 500);
        frame.add(gamePanel);
        frame.setVisible(true);
    }
}
