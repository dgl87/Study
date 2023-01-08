# SERILOG
https://serilog.net/
> O Serilog é um framework que fornece registro de diagnóstico ou Logging para arquivos, banco de dados, console e em outros fornatos, facilitando a criação de registros de logs; é fácil de configurar, tem uma API limpa e é portátil entre as plataformas .NET.

## Serilog - Instalação
Pacote Nuget principal ***Serilog***
```
Package Manager - Install-Package Serilog -Version 2.10.0

NET CLI - dotnet add package Serilog --version 2.10.0
```
Pacote Nuget para ASP.NET Core Serilog.AspNetCore
```
Package Manager - Install-Package Serilog.AspNetCore - Version 4.1.0

NET CLI - dotnet add package Serilog.AspNetCore --version 4.1.0
```

Pacotes Nuget para os coletores ou sinks:
https://github.com/serilog/serilog/wiki/Provided-Sinks
```
Install-Package Serilog.Sinks.Console
Install-Package Serilog.Sinks.File
Install-Package Serilog.Sinks.MSSqlServer
```

Pacotes Nuget para os enriquecedores ou enrichers
```Shell
Install-Package Serilog.Enrichers.Environment
Install-Package Serilog.Enrichers.Thread
Install-Package Serilog.Enrichers.Process
```

## Serilog - Nível de log
- ***Verbose:*** Descreve informações detalhadas de _debug_ e rastreamento
- ***Debug:*** Descreve informações de debug e fluxo da aplicação (eventos internos)
- ***Information:*** Registra eventos de interesse e relevância (funções do sistema)
- ***Warning:*** Registra informações de _possíveis_ problemas e comportamento inesperado
- ***Error:*** Registra informações de falhas de qualqeur tipo, usado em exceções
- ***Fatal:*** Registra erros críticos que comprometem a aplicação de forma completa

## Serilog - Exemplo com ASP.NET CORE
> Aplicação ASP.NET Core com Serilog: Criar registros de log no console, em arquivo e em um banco de dados SQL Server

Pacotes Nuget usados:
* Serilog.AspNetCore
* Serilog.Sinks.MSSqlServer
* Serilog.Enrichers.Thread
* Serilog.Enrichers.Process
* Serilog.Enrichers.Environment

## Roteiro
1. Configurar o Serilog no arquivo Program.cs;
2. Criar no arquivo appsettings.json as definições para os logs desejados;
3. Invocar o método para criação do registro de Log

## 1. Serilog - Configurar o Serilog no arquivo Program.cs
Packages
```C#
Serilog.AspNetCore
Serilog.Enrichers.Environment
Serilog.Enrichers.Process
Serilog.Enrichers.Thread
Serilog.Sinks.MSSqlServer
```

## Program.cs
```diff
public class Program
    {
        public static void Main(string[] args)
        {
+           var config = new ConfigurationBuilder()
+               .AddJsonFile("appsettings.json").Build();       // Lê as definições do arquivo appsettings.json

+           Log.Logger = new LoggerConfiguration()
+               .ReadFrom.Configuration(config).CreateLogger(); /* O Usamos uma instancia de LoggerConfiguration e a partir das informações obtidas no 
+                                                                * arquivo appsettings.json criamos um logger usando os sinks, os enrichers e as demais definições
+                                                                */

+           try
+           {
+               Log.Information("API inicializando...");
+               CreateHostBuilder(args).Build().Run();
+               CreateHostBuilder(args).Build().Run();
+           }
+           catch (Exception ex)
+           {
+               Log.Fatal(ex, "A aplicação falhou ao iniciar.");
+           }
+           finally
+           {
+               Log.CloseAndFlush();
+           }
+       }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
+           .UseSerilog()                                       // Define o Serilog como provedor de Log
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

## appsettings.json
```diff
+{  
+  "AllowedHosts": "*",
+  "Serilog": {                  //Cria a seção Serilog
+    "Using": [],
+    "MinimumLevel": {
+      "Default": "Information",  // Define configurações mínimas
+     "Override": {
+        "Microsoft": "Warning",
+        "System": "Warning"
+      }
+    },
+    "WriteTo": [                 // Define o log para o Console
+      {
+        "Name": "Console"
+      },
+      {
+        "Name": "File",
+        "Args": {
+          "path": "C:\\Dados\\Logs\\log.txt",
+          "outputTemplate": "{Timestamp} {Message}{NewLine:1}{Exception:1}"
+        }
+      },
+      {
+        "Name": "File",
+        "Args": {
+          "path": "C:\\Dados\\Logs\\log.json",
+          "formatter": "Serilog.Formatting.Json.JsonFormatter, Serilog"
+        }
+      },
```
      Define o log para um arquivo 

      Definimos dois logs para arquivo texto e outro para arquivo json definindo o caminho e nome dos arquivos
     
```diff     
+     {
+       "Name": "MSSqlServer",    // Define o registro do log para o banco de dados SQL Server
+       "Args": {                 // Definimos a string de conexão, o nome da tabela e o nível mínimo de registro
+         "connectionString": "Server=localhost,1433;Database=Logs;User ID=sa;Password=2w3e4r5t!@#;TrustServerCertificate=true",
+         "sinkOptionsSection": {
+           "tableName": "Logs",
+           "autoCreateSqlTable": true
+         },
+         "restrictedToMinimumLevel": "Warning"
+       }
+     }
+   ],
+   "Enrich": [                   // Define os Enrichers usados para enriquecer os eventos de registros
+     "FromLogContext",           // 
+     "WithMachineName",          /// Incluir o nome da máquina
+     "WithProcessId",            /// Inclui o Id do processo
+     "WithThreadId"              /// Inclui o Id da Thread
+   ],
+   "Properties": {
+     "ApplicationName":  "Serilog.WebAPI"
+   }
  }
}
```

## Controller
```diff
[ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> _logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger)
        {
            _logger = logger;
+           _logger.LogInformation("WeatherForecast instanciando ILogger");
        }

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
+           _logger.LogInformation("endpoint GET -> WeatherForecast.Get() ");
+           int count;
+           try
+           {
+               for (count = 0; count <= 5; count++)
+               {
+                   if (count == 3)
+                       throw new Exception("Ocorreu uma Exception Aleatória... ");
+                   else
+                       _logger.LogInformation($"Número de iterações {count}");
+               }

                var rng = new Random();
                return Enumerable.Range(1, 5).Select(index => new WeatherForecast
                {
                    Date = DateTime.Now.AddDays(index),
                    TemperatureC = rng.Next(-20, 55),
                    Summary = Summaries[rng.Next(Summaries.Length)]
                })
                .ToArray();
            }
            catch (Exception ex)
            {
+               _logger.LogError(ex, $"endpoint GET -> WeatherForecast.Get() - Exception ");
                throw;
            }
        }
    }
```
