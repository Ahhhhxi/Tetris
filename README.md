package config;

import org.dom4j.Element;
import java.util.ArrayList;
import java.util.List;


public class FrameConfig
{
    private List<LaysConfig> laysConfigs;
    private final String  title;

    private final int width;
    private final int height;
    private final int windowSIZE;

    private final ButtonConfig buttonConfig;

    public FrameConfig(Element frame)
    {
        //拿frame参数
        this.width=Integer.parseInt(frame.attributeValue("width"));
        this.height=Integer.parseInt(frame.attributeValue("height"));
        this.windowSIZE=Integer.parseInt(frame.attributeValue("windowSIZE"));
        this.title= frame.attributeValue("title");

        List<org.dom4j.Element> lays= frame.elements("lays");
        laysConfigs =new ArrayList<LaysConfig>();
        //拿lays参数
        for (org.dom4j.Element layer:lays)
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

        buttonConfig=new ButtonConfig(frame.element("button"));
    }


    public String getTitle() {
        return title;
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

    public ButtonConfig getButtonConfig() {
        return buttonConfig;
    }
}

