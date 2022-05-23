package config;

import org.dom4j.Element;

import java.util.List;

public class DataConfig
{
    private final dataInterfaceConfig dataA;
    private final dataInterfaceConfig dataB;

    public DataConfig(Element data)
    {
        dataA=new dataInterfaceConfig(data.element("dataA"));
        dataB=new dataInterfaceConfig(data.element("dataB"));
    }

    public dataInterfaceConfig getDataA() {
        return dataA;
    }

    public dataInterfaceConfig getDataB() {
        return dataB;
    }
}

