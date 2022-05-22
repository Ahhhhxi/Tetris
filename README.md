
package service;

import dto.GameDto;
import dto.Player;
import entity.GameAct;

import java.awt.*;
import java.util.Arrays;
import java.util.List;
import java.util.Random;

//游戏逻辑
public class GameService
{
    private Random random=new Random();

    private static final int TYPE_RANGE=7;


    private GameDto dto;

    public GameService(GameDto dto)
    {
        this.dto=dto;
    }



    public boolean keyDown()
    {

        //判断移动是否成功
        if(this.dto.getGameAct().move(0,1,this.dto.getGameMap()))
        {
            return false;
        }
        boolean[][] map=this.dto.getGameMap();
        Point[] act=this.dto.getGameAct().getActPoint();
        for(int i=0;i<this.dto.getGameAct().getActPoint().length;i++)
        {
            map[act[i].x][act[i].y]=true;
        }

        //是否消去 消去
        this.dto.setGameMap(eraseCube(map,this.dto.getGameAct().getActPoint()));
        this.dto.setRemoveLine(0);

            //计算分数
            //一定分数可以换个背景



        //刷新新方块 7个一循环
        //将下一个块生成
        this.dto.getGameAct().init(this.dto.getNext());
        this.dto.setNext(cubeCodeInit(this.dto.getCubeInitCycle(),random.nextInt(TYPE_RANGE)));

        if(checkLose())
        {
            afterLose();
        }
        return true;

    }

    public void suddenFall()
    {
        while (!keyDown()) {}
    }

    private void afterLose()
    {
        //设置开始状态
        this.dto.setStart(false);

        //关闭主线程

    }

    public void keyLeft()
    {
        this.dto.getGameAct().move(-1,0,this.dto.getGameMap());
    }

    public void keyRight()
    {
        this.dto.getGameAct().move(1,0,this.dto.getGameMap());
    }
/*
    public void keyASpin()
    {
        Point[] center=this.dto.getGameAct().getActPoint();
            this.dto.getGameAct().spinLeft(center[0].x,center[0].y,this.dto.getGameMap());
    }
*/

    public void keyXSpin()
    {
        synchronized (this.dto)
        {
            Point[] center=this.dto.getGameAct().getActPoint();
            this.dto.getGameAct().spinRight(center[0].x,center[0].y,this.dto.getGameMap());
        }

    }

    public void keyPause()
    {
        this.dto.setPause();
    }

/*
    private boolean canMove(int moveX,int moveY)
    {
        Point[] nowPoints= this.dto.getGameAct().getActPoint();

        for(int i=0;i<nowPoints.length;i++)
        {
            int newX=nowPoints[i].x+moveX;
            int newY=nowPoints[i].y+moveY;
            if(newX<0||newX>9||newY>17)
                return false;
        }
        return true;
    }

*/


    private int cubeCodeInit(boolean[] list, int code)
    {
        int index=0;
        for(int i=0;i<list.length;i++)
        {
            if(list[i]==true)
                index++;
        }
        if(index== list.length)
        {
            boolean[] re=new boolean[list.length];
            this.dto.setCubeInitCycle(re);
            return code;
        }

        if(list[code]==false)
        {
            list[code]=true;
            this.dto.setCubeInitCycle(list);
            return code;
        }
        else
        {
            return cubeCodeInit(list, random.nextInt(TYPE_RANGE));
        }
    }


    private boolean[][] eraseCube(boolean[][] map,Point[] points)
    {
        int[] eraseRow=new int[points.length];
        int rows=0;
        for (int i=0;i< points.length;i++)
        {
            int index=0;
            for (int j=0;j<10;j++)
            {
                if(map[j][points[i].y]==true)
                    index++;
            }
            if(index==10)
            {
                eraseRow[i]=points[i].y;
                rows++;
            }

        }
        eraseRow=eraseRepeat(eraseRow,rows);
        return reArrange(map,eraseRow);

    }

    private boolean[][] reArrange(boolean[][] map,int[] row)//消除行具体位置
    {
        Arrays.sort(row);
        for (int i=0;i<row.length;i++)
        {
            if(row[i]!=0)
            {
                for(int j=row[i];j>0;j--)
                {
                    for (int k=0;k<10;k++)
                    {
                        map[k][j]=map[k][j-1];
                    }
                }
            }
        }


        return map;
    }


    private int[] eraseRepeat(int[] array,int rows)
    {
        int com=-1;//比较数
        int index=0;
        for(int i=0;i<array.length;i++)
        {
            if(array[i]!=com)
                com=array[i];
            else if(array[i]!=0)
            {
                array[i]=0;
                index++;
            }

        }

        this.dto.setRemoveLine(rows-index);
        this.dto.setTotalRemoveLine(this.dto.getTotalRemoveLine()+this.dto.getRemoveLine());
        this.dto.setNowPoint(this.dto.getNowPoint()+this.dto.getRemoveLine()*500*this.dto.getRemoveLine());
        if(this.dto.getStopTime()-this.dto.getTotalRemoveLine()*10>50)
            this.dto.setStopTime(this.dto.getStopTime()-this.dto.getTotalRemoveLine()*10);

        return array;
    }

    public boolean checkLose()
    {
        Point[] points=this.dto.getGameAct().getActPoint();

        boolean[][] nowMap=this.dto.getGameMap();

        for (int i=0;i< points.length;i++)
        {
            if(nowMap[points[i].x][points[i].y])
                return true;
        }
        return false;
    }


    //测试用---------------------------------------------
/*
    public void setRecodeDataBase(List<Player> players)
    {
        this.dto.setDbRecode(players);
    }
*/
    public void setRecodeDisk(List<Player> players)
    {
        this.dto.setDiskRecode(players);
    }

    public void start()
    {
        this.dto.setNext(cubeCodeInit(this.dto.getCubeInitCycle(),random.nextInt(TYPE_RANGE)));
        GameAct act=new GameAct(cubeCodeInit(this.dto.getCubeInitCycle(),random.nextInt(TYPE_RANGE)));
        dto.setGameAct(act);
        this.dto.setStart(true);
        //初始化
        this.dto.dtoInit();
    }

}
