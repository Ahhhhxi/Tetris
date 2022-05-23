package config;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.dom4j.Element;

public class dataInterfaceConfig
{
    private final String className;
    private  final Map<String,String> param;

    public dataInterfaceConfig(Element data)
    {
        this.className=data.attributeValue("className");
        this.param=new HashMap<String,String>();

        List<Element> params=data.elements("param");
        for (Element e:params)
        {
            this.param.put(e.attributeValue("key"),e.attributeValue("value"));
        }

    }

    public String getClassName(){
        return className;
    }

    public Map<String, String> getParam() {
        return param;
    }
}
