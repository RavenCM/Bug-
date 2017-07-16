## MongoTemplate自定义Converter

#### 起因
有一个Entity需要在MySQL和MongoDB中进行保存，MySQL中为了记录时分秒使用了DateTime数据类型，在Java中对应的数据类型是Timestamp，当把这个数据类型存入MongoDB中时，MongoDB默认转成了Date类型，当从MongoDB中读取数据时会抛出异常说找不到java.util.Date转java.sql.Timestamp的转换器。

#### 详细异常信息
```Java
org.springframework.core.convert.ConverterNotFoundException: No converter found capable of converting from type [java.util.Date] to type [java.sql.Timestamp]
```

#### 解决方法(1)：自定义Converter
1. 自定义ReadingConverter
```Java
import org.springframework.core.convert.converter.Converter;

import java.sql.Timestamp;
import java.util.Date;

public class TimestampConverter implements Converter<Date, Timestamp> {
    @Override
    public Timestamp convert(Date date) {
        return new Timestamp(date.getTime());
    }
}
```
2. 将自定义的Conterver生效
```Java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.convert.converter.Converter;
import org.springframework.data.mongodb.core.convert.CustomConversions;
import java.util.ArrayList;
import java.util.List;

@Configuration
public class MongoConfiguration{

    @Bean
    public CustomConversions customConversions() throws Exception {
        List<Converter<?, ?>> converterList = new ArrayList<>();
        converterList.add(new TimestampConverter());
        return new CustomConversions(converterList);
    }
}
```