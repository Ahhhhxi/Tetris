package UI;

import Control.GameControl;
import Control.PlayerControl;
import config.FrameConfig;
import config.GameConfig;
import config.LaysConfig;
import dao.DataDisk;
import dto.GameDto;
import service.GameService;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.lang.reflect.Constructor;
import java.util.ArrayList;
import java.util.List;


public class JPanelGame extends JPanel
{
    private static final int BTN_SIZE_W=GameConfig.getFrameConfig().getButtonConfig().getButtonW();
    private static final int BTN_SIZE_H=GameConfig.getFrameConfig().getButtonConfig().getButtonH();
    private static final int BTN_START_X=GameConfig.getFrameConfig().getButtonConfig().getStartX();
    private static final int BTN_START_Y=GameConfig.getFrameConfig().getButtonConfig().getStartY();
    private static final int BTN_SET_X=GameConfig.getFrameConfig().getButtonConfig().getUserConfigX();
    private static final int BTN_SET_Y=GameConfig.getFrameConfig().getButtonConfig().getUserConfigY();


    private List<Lays> lays=null;

    private GameDto dto=null;

    private JButton btnStart;

    private  JButton btnConfig;

    private GameControl gameControl=null;

    private DataDisk dataB=null;

    private void initButton()
    {
        this.btnStart=new JButton("Start");
        btnStart.setBounds(BTN_START_X,BTN_START_Y,BTN_SIZE_W,BTN_SIZE_H);
        this.add(btnStart);

        this.btnConfig=new JButton("Set");
        btnConfig.setBounds(BTN_SET_X,BTN_SET_Y,BTN_SIZE_W,BTN_SIZE_H);
        this.add(btnConfig);

        this.btnStart.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                boolean[][] test=dataB.readArray();
                if(test!=null)
                    dto.setSaveStatus(true);
                if(dto.isSaveStatus())
                {

                    JFrameIfLoad load=new JFrameIfLoad(gameControl,dto);
                    load.setVisible(true);
                }
                else
                gameControl.start();
            }
        });



        this.btnConfig.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                JFrameSet frameSet=new JFrameSet(dto,gameControl);
                frameSet.setVisible(true);
                if(dto.isStart())
                    gameControl.keyPause();
            }
        });

    }

    public JPanelGame(GameDto dto,GameControl gameControl,DataDisk dataB)
    {
        this.dto=dto;
        this.setLayout(null);
        this.gameControl=gameControl;
        this.dataB=dataB;
        this.initComponent(this.gameControl);
        initLays();
        initButton();


/*     硬编码
        lays=new Lays[]
                {
                        new LayBackground(0,0,0,0),
                        new LayWindows(40,32,334,279),
                        new LayWindows(40,343,334,279),
                        new LayWindows(414,32,334,590),
                        new LayWindows(778,32,334,124),
                        new LayWindows(778,188,158,148),
                        new LayWindows(964,188,158,148),
                        new LayWindows(788,368,334,200)
                };
                */
    }


    public void initLays()
    {
        try
        {
            FrameConfig fCFG=GameConfig.getFrameConfig();

            //获得层配置
            List<LaysConfig> laysConfig=fCFG.getLaysConfigs();
            //创建所以层对象
            lays=new ArrayList<Lays>(laysConfig.size());

            for (LaysConfig laysConfig1:laysConfig)
            {
                //获得类对象
                Class<?> c= Class.forName((laysConfig1.getClassName()));
                //获得构造函数
                Constructor ctr= c.getConstructor(int.class,int.class,int.class,int.class);
                //调用构造函数 创建对象
                Lays l=(Lays) ctr.newInstance(
                        laysConfig1.getX(),
                        laysConfig1.getY(),
                        laysConfig1.getW(),
                        laysConfig1.getH()
                );
                //设置游戏数据对象
                l.setDto(this.dto);
                lays.add(l);

            }

        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }

    public void buttonSwitch(boolean on)
    {
        this.btnStart.setEnabled(on);
    }



    public void initComponent(GameControl gameControl)
    {
        //创建游戏控制器
        //控制 jpanel 与 game_service
        this.addKeyListener(new PlayerControl(gameControl));
    }

    //安装玩家控制器
    public void setGameControl(PlayerControl control)
    {
        this.addKeyListener(control);
    }


        //画面板
    @Override
    public void paintComponent(Graphics g)
    {
        super.paintComponent(g);

        for (int i=0;i<lays.size();i++)
            lays.get(i).paint(g);
        //保持焦点在panel上
        this.requestFocus();

    }

    public void setGameControl(GameControl gameControl)
    {
        this.gameControl = gameControl;
    }
}
