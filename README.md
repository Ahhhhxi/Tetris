package config;

import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.util.ArrayList;
import java.util.List;

//读取  g.xml 文件

public class GameConfig
{
    private  int width;
    private  int height;
    private  int windowSIZE;
    private String title;


    private List<LaysConfig> laysConfigs;

    public static FrameConfig FRAME_CONFIG=null;

    public static SystemConfig SYSTEM_CONFIG=null;

    public static DataConfig DATA_CONFIG=null;

    static
    {
        try {
            SAXReader reader= new SAXReader();
            Document doc= reader.read("config/g.xml");
            Element game= doc.getRootElement();
            //配置game


            //创建配置对象
            FRAME_CONFIG=new FrameConfig(game.element("frame"));
            DATA_CONFIG=new DataConfig(game.element("data"));
            SYSTEM_CONFIG=new SystemConfig(game.element("system"));
        }catch (Exception e)
        {
            e.printStackTrace();
        }



    }

    private GameConfig()
    {

    }

    public static FrameConfig getFrameConfig()
    {
        return FRAME_CONFIG;
    }

    public static DataConfig getDataConfig()
    {
        return DATA_CONFIG;
    }
    public static SystemConfig getSystemConfig()
    {
        return SYSTEM_CONFIG;
    }

    private void setUIConfig(Element frame)
    {
        List<Element> lays= frame.elements("lays");
        laysConfigs =new ArrayList<LaysConfig>();
        //拿frame参数
        this.width=Integer.parseInt(frame.attributeValue("width"));
        this.height=Integer.parseInt(frame.attributeValue("height"));
        this.windowSIZE=Integer.parseInt(frame.attributeValue("windowSIZE"));
        this.title= frame.attributeValue("title");

        //拿lays参数
        for (Element layer:lays)
        {
            LaysConfig lc =new LaysConfig
                    (
                    layer.attributeValue("class"),
                    Integer.parseInt(layer.attributeValue("x") ),
                    Integer.parseInt(  layer.attributeValue("y")),
                    Integer.parseInt(layer.attributeValue("w")),
                    Integer.parseInt( layer.attributeValue("h"))
            );


            laysConfigs.add(lc);
        }
    }

    private void setSystemConfig(Element frame)
    {
        //TODO
    }

    private void setDataConfig(Element data)
    {
        //TODO
    }


    public int getWidth() {
        return width;
    }

    public int getHeight() {
        return height;
    }

    public int getWindowSIZE() {
        return windowSIZE;
    }

    public List<LaysConfig> getLaysConfigs() {
        return laysConfigs;
    }

    public String getTitle() {
        return title;
    }
}
