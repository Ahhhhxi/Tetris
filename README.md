package entity;

import java.awt.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class GameAct {

    public Point[] getActPoint() {
        return actPoint;
    }

    private Point[] actPoint;
    private int cubeCode;


    private static final List<Point[]> TYPE_CONFIG;
    static
    {
        TYPE_CONFIG=new ArrayList<Point[]>(7);
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,0), new Point(5,0), new Point(5,1),});
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,0), new Point(5,0), new Point(3,1),});
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,0), new Point(5,0), new Point(4,1),});
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,0), new Point(5,0), new Point(6,0),});
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,0), new Point(4,1), new Point(5,1),});
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,1), new Point(4,1), new Point(5,0),});
        TYPE_CONFIG.add(new  Point[]{new Point(4,0), new Point(3,0), new Point(3,1), new Point(4,1),});
    }

    public GameAct(int random)
    {
        //之后写道配置文件里
        init(random);
    }


    public boolean move(int moveX,int moveY,boolean[][] gameMap)
    {

        for(int i=0;i<actPoint.length;i++)
        {
            if(checkMap(actPoint[i].x+moveX,actPoint[i].y+moveY,gameMap))
                return false;
        }

        for(int i=0;i<actPoint.length;i++)
        {
            actPoint[i].x=actPoint[i].x+moveX;
            actPoint[i].y=actPoint[i].y+moveY;
        }
        return true;
    }

    public void init(int cubeCode)
    {
        this.cubeCode=cubeCode;

        Point[] points=TYPE_CONFIG.get(cubeCode);
        actPoint=new Point[points.length];
        for (int i=0;i<points.length;i++)
        {
            actPoint[i]=new Point(points[i].x,points[i].y);
        }
    }
/*
    public void spinLeft(int centerX,int centerY, boolean[][] gameMap)
    {
        if(this.cubeCode==6)
            return;

        for(int i=1;i<actPoint.length;i++)
        {
            int newX=-centerX+centerY-actPoint[i].y;
            int newY=-centerY-centerX+actPoint[i].x;

            if(checkMap(newX,newY,gameMap));
                return ;
        }
        for(int i=1;i<actPoint.length;i++)
        {
            int newX=centerX+centerY-actPoint[i].y;
            int newY=centerY-centerX+actPoint[i].x;
            actPoint[i].x=newX;
            actPoint[i].y=newY;
        }

    }
*/
    public void spinRight(int centerX,int centerY, boolean[][] gameMap)
    {
        if(this.cubeCode==6)
            return;
        for(int i=1;i<actPoint.length;i++)
        {
            int newX=centerX-centerY+actPoint[i].y;
            int newY=centerY+centerX-actPoint[i].x;
            if(checkMap(newX,newY,gameMap))
                return;
        }
        for(int i=1;i<actPoint.length;i++)
        {
            int newX=centerX-centerY+actPoint[i].y;
            int newY=centerY+centerX-actPoint[i].x;
            actPoint[i].x=newX;
            actPoint[i].y=newY;
        }


    }

    private boolean checkMap(int x, int y,boolean[][] gameMap)
    {
        return x<0||x>9||y>17||y<0||gameMap[x][y];
    }

    public  int getCubeCode()
    {
        return cubeCode;
    }
}

