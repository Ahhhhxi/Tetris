# Tetris
java project
package Control;

import UI.JFrameGame;
import UI.JFrameSavePoint;
import UI.JPanelGame;
import config.GameConfig;
import config.dataInterfaceConfig;
import dao.Data;
import dao.DataDisk;
import dto.GameDto;
import dto.Player;
import service.GameService;

import java.lang.reflect.Constructor;
import java.util.HashMap;
//接受玩家控制
//控制面板布局
//控制游戏逻辑


public class GameControl
{

    //游戏界面
    private JPanelGame jPanelGame;

    //保存分数窗口
    private JFrameSavePoint frameSavePoint;

    //游戏逻辑
    private GameService gameService;

    //
    private Data dataA;

    private DataDisk dataB=new DataDisk();

    private GameDto dto;
    //游戏线程
    private Thread gameThread=null;

    public GameControl()
    {
        dto=new GameDto();
        this.dto.setDataB(this.dataB);

        this.gameService=new GameService(dto);

        this.dataA=createDataObject(GameConfig.getDataConfig().getDataA());
        //this.dataB=createDataObject(GameConfig.getDataConfig().getDataB());

        this.dto.setDbRecode(dataA.loadData());
        //this.dto.setDiskRecode(dataB.loadData());

        this.jPanelGame=new JPanelGame(dto,this,this.dataB);

        this.frameSavePoint =new JFrameSavePoint(this);

        new JFrameGame(this.jPanelGame);
    }

    private Data createDataObject(dataInterfaceConfig cfg)
    {
        try {
            Class<?> cls=Class.forName(cfg.getClassName());
            //获得构造器
            Constructor<?> ctr= cls.getConstructor(HashMap.class);
            //创建对象
            return (Data) ctr.newInstance(cfg.getParam());
        }
        catch (Exception e)
        {
            e.printStackTrace();
            return null;
        }
    }

    public void keyDown()
    {
        if(dto.isPause())
            return;

        this.gameService.keyDown();
        this.jPanelGame.repaint();
    }
    public void keyLeft()
    {
        if(dto.isPause())
            return;
        this.gameService.keyLeft();
        this.jPanelGame.repaint();
    }
    public void keyRight()
    {
        if(dto.isPause())
            return;
        this.gameService.keyRight();
        this.jPanelGame.repaint();
    }
    /*
    public void keyASpin()
    {
        this.gameService.keyASpin();
        this.jPanelGame.repaint();
    }
    */
    public void keyXSpin()
    {
        if(dto.isPause())
            return;
        this.gameService.keyXSpin();
        this.jPanelGame.repaint();
    }

    public void keyPause()
    {
        this.gameService.keyPause();
        this.jPanelGame.repaint();
    }

    public void start()
    {
        //面板按钮不可点击
        this.jPanelGame.buttonSwitch(false);

        this.frameSavePoint.setVisible(false);

        //游戏开始
        this.gameService.start();
        this.jPanelGame.repaint();
        //创建线程对象
        this.gameThread=new MainThread();

        this.gameThread.start();
        this.jPanelGame.repaint();
    }

    public void suddenFall()
    {
        if(dto.isPause())
            return;
        this.gameService.suddenFall();
        this.jPanelGame.repaint();
    }

    public void savePoint(String name)
    {
        Player player=new Player(name,this.dto.getNowPoint());
        this.dataA.saveData(player);
        //this.dataB.saveData(player);
        this.dto.setDbRecode(dataA.loadData());
        //this.dto.setDiskRecode(dataB.loadData());
        this.jPanelGame.repaint();
    }

    public void saveMap()
    {
        this.dataB.saveArray(this.dto.getGameMap());
        this.dto.setSaveStatus(true);
    }

    public void loadMap(){this.dto.setGameMap(this.dataB.readArray());}

    public void afterLose()
    {
        //save point
        this.frameSavePoint.show(this.dto.getNowPoint());
        ////can press start
        this.jPanelGame.buttonSwitch(true);
    }

    private class MainThread extends Thread{
        @Override
        public void run()
        {



            while (dto.isStart())
            {
                jPanelGame.repaint();

                //主循环
                try {
                    //等1s
                    Thread.sleep(dto.getStopTime());
                    //若暂停则不执行
                    if(dto.isPause())
                        continue;

                    //方块下落
                    gameService.keyDown();
                    jPanelGame.repaint();

                }catch (Exception e)
                {
                    e.printStackTrace();
                }

            }
            afterLose();
        };

    }

}
