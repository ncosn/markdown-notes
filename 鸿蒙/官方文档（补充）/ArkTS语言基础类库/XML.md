#### XML生成

XML用于描述数据的标记语言，由元素（element）、属性（attribute）和内容（content）组成。

+ 元素指的是标记对，包含文本、属性或其他元素。
+ 属性提供了有关元素的其他信息。
+ 内容则是元素包含的数据或子元素。

XML模块提供XmlSerializer类来生成XML文件，输入为固定长度的Arraybuffer或DataView对象，该对象用于存放输出的XML数据。

```ts
// 1.引入模块
import xml from '@ohos.xml'
import util from '@ohos.util'

@Entry
@Component
struct Xml {
    @State message: string = '';
    
    aboutToAppear() {
        // 2.基于DataView的XmlSerialzer对象
        let arrayBuffer: ArrayBuffer = new ArrayBuffer(2048); // 创建一个2048字节的缓冲区
        let dataView: DataView = new DataView(arrayBuffer);// 使用DataView对象操作ArrayBuffer对象
        let thatSer: xml.XmlSerializer = new xml.XmlSerializer(dataView);// 基于DataView构造XmlSerializer对象
        
        thatSer.setDeclaration();// 写入xml的声明
        thatSer.startElement('bookstore');// 写入元素开始标记
        thatSer.startElement('book');// 嵌套元素开始标记
        thatSer.setAttributes('category','COOKING');// 写入属性及属性值
        thatSer.startElement('title');
        thatSer.setAttributes('lang','en');
        thatSer.setText('Everyday');// 写入标签值
        thatSer.endElement();// 写入结束标记
        thatSer.startElement('author');
        thatSer.setText('Giada');
        thatSer.endElement();
        thatSer.startElement('year');
        thatSer.setText('2005');
        thatSer.endElement();
        thatSer.endElement();
        thatSer.endElement();
        
        let view: Unit8Array = new Uint8Array(arrayBuffer);// 使用Uint8Array读取arrayBuffer的数据
        let textDecoder: util.TextDecoder = util.TextDecoder.create();// 调用util模块的TextDecoder类
        let res: string = textDecoder.decodeWithStream(view);// 对view解码
        console.info(res);
    }
}
```



#### XML解析

XML模块提供XmlPullParser类对XML文件解析，输入为含有XML文本的ArrayBuffer或DataView，输出为解析得到的信息

```ts
parseXml() {
    let strXml: string = 
        '<?xml version="1.0" encoding="utf-8"?>' +
        '<note importance="high" logged="true">' +
        '<title>Play</title>' +
        '</note?>';
    let textEncoder: util.TextEncoder = new util.TextEncoder();
    let arrBuffer: Uint8Array = textEncoder.encodeInto(strXml);//对数据编码，防止包含中文字符乱码
    // 1.基于ArrayBuffer构造XmlPullParser对象
    let that: xml.XmlPullParser = new xml.XmPullParser(arrBuffer.buffer, 'UTF-8');
    
    let options: xml.ParseOptions = {
        supportDoctype: true,
        ignoreNameSpace: true,
        tokenValueCallbackFunction: (key: xml.EventType, value: xml.ParseInfo) => {
            let str = 'key:' + key + ' | name:' + value.getName() + ' | value:' + value.getText();
            console.info(str)
            return true;
        }
    };
    that.parse(option);
}
```



#### XML转换

将XML文本转换成JavaScript对象可以更轻松地处理和操作数据，并且更适合在JavaScript应用程序中使用。

```ts
convertXML() {
    let xml: string = 
        '<?xml version="1.0" encoding="utf-8"?>' +
        '	<note importance="high" logged="true">' +
        '		<title>Happy</title>' +
        '		<todo>Work</todo>' +
        '		<todo>Play</todo>' +
        '</note>';
    
    let options: convertxml.ConverOptions = {
        trim: false,
        declarationKey: "_declaration",
        instructionKey: "_instruction",
        attributeKey: "_attributes",
        textKey: "_text",
        cdataKey: "_cdata",
        doctypeKey: "_doctype",
        commentKey: "_comment",
        parentKey: "_parent",
        typeKey: "_type",
        nameKey: "_name",
        elementsKey: "_elements"
    }
    
    let convL convertxml.ConvertXML = new convertxml.ConvertXML();
    let result: object = conv.converToJSObject(xml, options);
    let strRes: string = JSON.stringify(result);//将js对象转换为json字符串，用于显示输出
    console.info(strRes);
}
```

