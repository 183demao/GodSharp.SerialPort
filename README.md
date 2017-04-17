# GodSharp.SerialPort
An easy-to-use .NET SerialPort class.

[![AppVeyor build status](https://img.shields.io/appveyor/ci/seayxu/godsharp-serialport.svg?label=appveyor&style=flat-square)](https://ci.appveyor.com/project/seayxu/godsharp-serialport/) [![NuGet](https://img.shields.io/nuget/v/GodSharp.SerialPort.svg?label=nuget&style=flat-square)](https://www.nuget.org/packages/GodSharp.SerialPort/) [![MyGet](https://img.shields.io/myget/seay/v/GodSharp.SerialPort.svg?label=myget&style=flat-square)](https://www.myget.org/Package/Details/seay?packageType=nuget&packageId=GodSharp.SerialPort)


# Requirement
.NET Framework >= 3.5

# Getting Started

1. New instance GodSerialPort.

```
GodSerialPort serial = new GodSerialPort("COM1", 9600);
```

About params:

- Parity value.

  - Parity.Space��0|s|space
  - Parity.Mark��1|m|mark
  - Parity.Even��2|e|even
  - Parity.Odd��3|o|odd
  - Parity.None��4|n|none

- StopBits value.

  - StopBits.None��0|n|none
  - StopBits.One��1|o|one
  - StopBits.OnePointFive��3|opf|of|f
  - StopBits.Two��2|t|two

- Handshake value.

  - Handshake.None��0|n|none
  - Handshake.RequestToSend��1|r|rst
  - Handshake.RequestToSendXOnXOff��2|rtsxx|rsxx|rtsx|rsx|rx
  - Handshake.XOnXOff��3|x|xx

2. Use `DataReceived` event with received data action: `Action<byte[]>`.

**Notice**:*This is not need when you read data by read method.*
```
serial.UseDataReceived((bytes)=>{});
```

3. Open SerialPort object.

```
serial.Open();
```

4. Write/Send data.

```
byte[] bytes = new byte[]{31,32,33,34};
serial.Write(bytes);
serial.Write(bytes,offset:1,count:2);
serial.WriteHexString("7E 48 53 44");
serial.WriteAsciiString("ascii string");
```

5. Read data.
```
byte[] bytes = serial.Read();
string stringAsciiOrHex = serial.ReadString();
```

# Sample

```
class Program
{
    static void Main(string[] args)
    {
        Console.Write("input serialport number(only 0-9):");
        string read = Console.ReadLine();
        bool flag = uint.TryParse(read, out uint num);
        if (!flag)
        {
            Exit();
        }

        GodSerialPort gsp = new GodSerialPort("COM"+num, 9600);
        gsp.UseDataReceived((bytes) => {
             string buffer = string.Join(" ", bytes);
             Console.WriteLine("receive data:" + buffer);
        });
        flag = gsp.Open();

        if (!flag)
        {
            Exit();
        }

        Console.WriteLine("serialport opend");

        Console.WriteLine("press any thing as data to send,press key 'q' to quit.");

        string data = null;
        while (data == null || data.ToLower()!="q")
        {
            if (!string.IsNullOrEmpty(data))
            {
                Console.WriteLine("send data:"+data);
                gsp.WriteAsciiString(data);
            }
            data = Console.ReadLine();
        }
    }

    static void Exit()
    {
        Console.WriteLine("press any key to quit.");
        Console.ReadKey();
        Environment.Exit(0);
    }
}
```

# Notes

## 1.0.0
- The first version release.

## 1.0.1
- Fix ctor and comments.

## 1.1.0
- 1.Add UseDataReceived method use to trigger DataReceived event.
- 2.The read metnod can be used to end character.
- 3.Add sleep time when try read data.