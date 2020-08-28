# DotNetCore logging with NLog, logging in to database 
Sample code for NLog database logging step by step

Create a .Net core project. Then install NLog from NuGet, We are installing NLog for .Net Core and NLog config for adding configuration, after that SqlClient for NLog Database connectivity

```
Install-Package NLog.Web.AspNetCore
Install-Package NLog.Config
Install-Package Microsoft.Data.SqlClient -Version 2.0.1
```
Now we are ready to start. we need to set the configuration now

```xml
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.nlog-project.org/schemas/NLog.xsd NLog.xsd"
      autoReload="true"
      throwExceptions="false"
      internalLogLevel="Info" internalLogFile="c:\temp\nlog-internal.log">
	<extensions>
		<add assembly="NLog.Web.AspNetCore"/>
	</extensions>
  <targets>
	  <target xsi:type="Database"
            name="dbTarget"
			dbProvider="Microsoft.Data.SqlClient.SqlConnection, Microsoft.Data.SqlClient"
            connectionString="Data Source=RAJU-DELL\SQLEXPRESS;Initial Catalog=DummyTable;Trusted_Connection=True;MultipleActiveResultSets=true;"
            commandText="INSERT INTO [DummyLogs](Message,Level,Exception,Trace,Logger,DateCreated) VALUES (@msg,@level,@exception,@trace,@logger,GETDATE())">
		  <parameter name="@msg" layout="${message}" />
		  <parameter name="@level" layout="${level}" />
		  <parameter name="@exception" layout="${exception}" />
		  <parameter name="@trace" layout="${trace}" />
		  <parameter name="@logger" layout="${logger}" />
	  </target>
  </targets>
  <rules>
	  <logger name="*" minlevel="Trace" writeTo="dbTarget" />
  </rules>
</nlog>
```

If the is any internal errors you can see in ```xml internalLogLevel="Info" internalLogFile="c:\temp\nlog-internal.log">```

From .NLog Git you can see how we can setup NLog for .Net core
https://github.com/NLog/NLog/wiki/Getting-started-with-ASP.NET-Core-3

I can add my SQL script for log table, In this table I am logging my result.
```SQL
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[DummyLogs](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[Message] [nvarchar](max) NOT NULL,
	[Level] [nvarchar](10) NOT NULL,
	[Exception] [nvarchar](max) NOT NULL,
	[Trace] [nvarchar](max) NOT NULL,
	[Logger] [nvarchar](max) NOT NULL,
	[DateCreated] [datetime] NULL,
 CONSTRAINT [PK_DummyLogs] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO
```



