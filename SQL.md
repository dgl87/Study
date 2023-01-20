--Cria banco de dados
```SQL
CREATE DATABASE [Curso]
```

--Apaga Banco de Dados
DROP DATABASE [Curso]

--Exclusão de uma Database
USE 
    [master]
GO

ALTER DATABASE [Blog] SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
GO

DROP DATABASE [Blog]
GO

--Finaliza processo e apaga o banco de dados
USE [master]

DECLARE @kill varchar(8000) = '';
SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'
FROM sys.dm_exec_sessions
WHERE database_id = db_id('Curso')

EXEC(@kill);

DROP DATABASE [Curso]

Criação de tabelas 
##CHAR() - Tamanho fixo de caracteres
##VARCHAR() - Varia o tamanho de caracteres
##NVARCHAR() - Suporta diferentes tipos de caracteres especiais
CREATE TABLE [Aluno](
    [Id] INT NOT NULL,
    [Nome] NVARCHAR(80) NOT NULL,
    [Email] NVARCHAR(180) NOT NULL UNIQUE,
    [Nascimento] DATETIME DEFAULT(GETDATE()),
    [Active] BIT DEFAULT(0)

    CONSTRAINT [PK_Aluno] PRIMARY KEY([Id]),
    CONSTRAINT [UQ_Aluno_Email] UNIQUE([Email])
)
GO

Apaga tabela
DROP TABLE [Aluno]

Altera uma tabela
--Adiciona uma coluna
ALTER TABLE [Aluno]
ADD [Document] NVARCHAR(11)

Remove uma coluna
ALTER TABLE [Aluno]
DROP COLUMN [Document]

Altera Id para ser PK
ALTER TABLE [Aluno]
ADD PRIMARY KEY([Id])

Altera coluna
ALTER TABLE [Aluno]
ALTER COLUMN [Active] BIT NOT NULL

CREATE TABLE [Curso](
    [Id] INT NOT NULL,
    [Nome] NVARCHAR(80) NOT NULL,
    [CategoriaId] INT NOT NULL
    CONSTRAINT [PK_Curso] PRIMARY KEY([Id]),
    CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY([CategoriaId])
    REFERENCES [Categoria]([Id])
)
GO

Tabela Associativa com chave composta
CREATE TABLE [ProgressoCurso](
    [AlunoId] INT NOT NULL,
    [CursoId] INT NOT NULL,
    [Progresso] INT NOT NULL,
    [UltimaAtualizacao] DATETIME NOT NULL DEFAULT(GETDATE())

    CONSTRAINT [PK_ProgressoCurso] PRIMARY KEY([AlunoId], [CursoId])
)
GO

CREATE TABLE [Categoria](
    [Id] INT NOT NULL,
    [Nome] NVARCHAR(80) NOT NULL

    CONSTRAINT [PK_Categoria] PRIMARY KEY([Id])
)
GO

Index - Para campos mais utilizados em query
CREATE INDEX [IX_Aluno_Email] ON [Aluno]([Email])

DROP INDEX [IX_Aluno_Email] ON [Aluno]

Identity - Adiciona +1 no último Id
CREATE TABLE [Curso](
    [Id] INT NOT NULL IDENTITY(1, 1),
    [Nome] NVARCHAR(80) NOT NULL,
    [CategoriaId] INT NOT NULL

    CONSTRAINT [PK_Curso] PRIMARY KEY([Id]),
    CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY([CategoriaId])
    REFERENCES [Categoria]([Id])
)
GO

UniqueIdentifier - Funciona como um Guid
SELECT NEWID()

CREATE TABLE [Curso](
    [Id] UNIQUEIDENTIFIER NOT NULL,
    [Nome] NVARCHAR(80) NOT NULL,
    [CategoriaId] INT NOT NULL

    CONSTRAINT [PK_Curso] PRIMARY KEY([Id]),
    CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY([CategoriaId])
    REFERENCES [Categoria]([Id])
)
GO

SCRIPT - Cria banco e tabelas
CREATE DATABASE [Cursos]
GO

USE [Cursos]

CREATE TABLE [Categoria](
    [Id] INT NOT NULL IDENTITY(1, 1),
    [Nome] NVARCHAR(80) NOT NULL
    CONSTRAINT [PK_Categoria] PRIMARY KEY([Id])
)
GO

CREATE TABLE [Curso](
    [Id] INT NOT NULL IDENTITY(1, 1),
    [Nome] NVARCHAR(80) NOT NULL,
    [CategoriaId] INT NOT NULL
    CONSTRAINT [PK_Curso] PRIMARY KEY([Id]),
    CONSTRAINT [FK_Curso_Categoria] FOREIGN KEY([CategoriaId])
    REFERENCES [Categoria]([Id])
)
GO

Inserindo registros nas tabelas
INSERT INTO [Categoria]([Nome]) VALUES('Backend')
INSERT INTO [Categoria]([Nome]) VALUES('Frontend')
INSERT INTO [Categoria]([Nome]) VALUES('Mobile')

INSERT INTO [Curso]([Nome], [CategoriaId]) VALUES('Fundamentos de C#', 1)
INSERT INTO [Curso]([Nome], [CategoriaId]) VALUES('Fundamentos de OOP', 1)
INSERT INTO [Curso]([Nome], [CategoriaId]) VALUES('Angular', 2)
INSERT INTO [Curso]([Nome], [CategoriaId]) VALUES('Flutter', 3)

DISTINCT / ORDER BY
SELECT TOP 2 * FROM [Curso]

SELECT DISTINCT TOP 100
    [Id], 
    [Nome],
    [CategoriaId]
FROM 
    [Curso]
WHERE 
    [Id] = 1 AND
    [CategoriaId] IS NOT NULL
    
SELECT TOP 100
    [Id], 
    [Nome],
    [CategoriaId]
FROM 
    [Curso]
-- WHERE     
--     [CategoriaId] = 1
ORDER BY
    [Nome], [Id], [CategoriaId] ASC
	
UPDATA -> ROLLBACK - COMMIT
BEGIN TRANSACTION
    UPDATE
        [Categoria] 
    SET 
        [Nome] = 'Azure'
    WHERE
        [Id] = 4
ROLLBACK

BEGIN TRANSACTION
    UPDATE
        [Categoria] 
    SET 
        [Nome] = 'Azure'
    WHERE
        [Id] = 4
COMMIT

DELETE
BEGIN TRANSACTION
    DELETE FROM 
        [Categoria]
    WHERE
        [Id] = 4
ROLLBACK
COMMIT

MIN MAX
SELECT TOP 100
    MIN([Id])
FROM    
    [Curso]

COUNT
SELECT TOP 100
    COUNT([Id])
FROM    
    [Curso]

AVG
SELECT TOP 100
    AVG([Id])
FROM    
    [Curso]

SUM
SELECT TOP 100
    SUM([Id])
FROM    
    [Curso]

LIKE
SELECT TOP 100
    *
FROM    
    [Curso]
WHERE   
    [Nome] LIKE '%Fundamentos%'

IN - Passa uma lista no Where
SELECT TOP 100
    *
FROM    
    [Curso]
WHERE   
    [Id] IN (1, 3)

BETWEEEN
SELECT TOP 100
    *
FROM    
    [Curso]
WHERE   
    [Id] BETWEEN '2020-03-01 00:00:00' AND '2020-03-31 23:59:00'

AS - Alias
SELECT TOP 100
    COUNT([Id]) AS [Total]
FROM
    [Curso]

INNER JOIN - Traz intersecção
SELECT TOP 100
    [Curso].[Id],
    [Curso].[Nome],
    [Categoria].[Id] AS [Categoria],
    [Categoria].[Nome]
FROM
    [Curso]
    INNER JOIN [Categoria] 
    ON [Curso].[CategoriaId] = [Categoria].[Id]

LEFT JOIN - Traz a tabela da esquerda
SELECT TOP 100
    [Curso].[Id],
    [Curso].[Nome],
    [Categoria].[Id] AS [Categoria],
    [Categoria].[Nome]
FROM
    [Curso]
    LEFT JOIN [Categoria] 
    ON [Curso].[CategoriaId] = [Categoria].[Id]

RIGHT JOIN - Traz a tabela da esquerda
SELECT TOP 100
    [Curso].[Id],
    [Curso].[Nome],
    [Categoria].[Id] AS [Categoria],
    [Categoria].[Nome]
FROM
    [Curso]
    RIGHT JOIN [Categoria] 
    ON [Curso].[CategoriaId] = [Categoria].[Id]

RIGHT JOIN - Traz a tabela da direita
SELECT TOP 100
    [Curso].[Id],
    [Curso].[Nome],
    [Categoria].[Id] AS [Categoria],
    [Categoria].[Nome]
FROM
    [Curso]
    FULL OUTER JOIN [Categoria] 
    ON [Curso].[CategoriaId] = [Categoria].[Id]
	
UNION
SELECT TOP 100
    [Id], [Nome]
FROM
    [Curso]
UNION ALL --ALL funciona como DISTINCT
SELECT TOP 100
    [Id], [Nome]
FROM
    [Categoria]

GROUP BY - Obrigatórios quando usado COUNT, AVG, SUM, MIN, MAX
SELECT TOP 100
    [Categoria].[Id],
    [Categoria].[Nome],
    COUNT([Curso].[CategoriaId]) AS [Cursos]
FROM
    [Categoria]
INNER JOIN [Curso] ON [Categoria].[Id] = [Curso].[CategoriaId] 
GROUP BY
    [Categoria].[Id],
    [Categoria].[Nome],
    [Curso].[CategoriaId]

HAVING - Filtro sobre o GROUP BY - Usado depois do GROUP BY
SELECT TOP 100
    [Categoria].[Id],
    [Categoria].[Nome],
    COUNT([Curso].[CategoriaId]) AS [Cursos]
FROM
    [Categoria]
INNER JOIN [Curso] ON [Categoria].[Id] = [Curso].[CategoriaId] 
GROUP BY
    [Categoria].[Id],
    [Categoria].[Nome],
    [Curso].[CategoriaId]
HAVING
    COUNT([Curso].[CategoriaId]) > 1

UNION ALL --ALL funciona como DISTINCT

SELECT TOP 100
    [Id], [Nome]
FROM
    [Categoria]

VIEW - Não pode ter WHERE

CREATE OR ALTER VIEW vw_ContagemCursosPorCategoria AS
    SELECT TOP 100
        [Categoria].[Id],
        [Categoria].[Nome],
        COUNT([Curso].[CategoriaId]) AS [Cursos]
    FROM
        [Categoria]
    INNER JOIN [Curso] ON [Categoria].[Id] = [Curso].[CategoriaId] 
    GROUP BY
        [Categoria].[Id],
        [Categoria].[Nome],
        [Curso].[CategoriaId]
    HAVING
        COUNT([Curso].[CategoriaId]) > 1

SELECT * FROM vw_ContagemCursosPorCategoria --Permite WHERE
WHERE [Nome] = 'Backend'

PROCEDURE - Script
CREATE OR ALTER PROCEDURE [sp_ListCourses] AS
    SELECT * FROM [Curso]

EXEC [sp_ListCourses]

DROP PROCEDURE [sp_ListCourses]

Variáveis
CREATE OR ALTER PROCEDURE [sp_ListCourses] 
    @Category NVARCHAR(60) --Parâmetro de Entrada
AS
    DECLARE @CategoryId INT
    SET @CategoryId = (SELECT TOP 1 [Id] FROM [Categoria]
                        WHERE [Nome] = @Category)

SELECT * FROM [Curso] WHERE [CategoriaId] = @CategoryId

EXEC [sp_ListCourses] 'Backend'

SCRIPT CREATE DATABASE DGLZ
CREATE DATABASE [DglZ]
GO

USE [DglZ]
GO

CREATE TABLE [Student]
(
    [Id] UNIQUEIDENTIFIER NOT NULL,
    [Name] NVARCHAR(120) NOT NULL,
    [Email] NVARCHAR(180) NOT NULL,
    [Document] NVARCHAR(20) NULL,
    [Phone] NVARCHAR(20) NULL,
    [Birthdate] DATETIME NULL,
    [CreateDate] DATETIME NOT NULL DEFAULT(GETDATE()),
    CONSTRAINT [PK_Student] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Author]
(
    [Id] UNIQUEIDENTIFIER NOT NULL,
    [Name] NVARCHAR(80) NOT NULL,
    [Title] NVARCHAR(80) NOT NULL,
    [Image] NVARCHAR(1024) NOT NULL,
    [Bio] NVARCHAR(2000) NOT NULL,
    [Url] NVARCHAR(450) NULL,
    [Email] NVARCHAR(160) NOT NULL,
    [Type] TINYINT NOT NULL,
    CONSTRAINT [PK_Author] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Career]
(
    [Id] UNIQUEIDENTIFIER NOT NULL,   
    [Title] NVARCHAR(160) NOT NULL,
    [Summary] NVARCHAR(2000) NOT NULL,
    [Url] NVARCHAR(1024) NULL,
    [DurationInMinutes] INT NOT NULL,
    [Active] BIT NOT NULL,
    [Featured] BIT NOT NULL,
    [Tags] NVARCHAR(160) NOT NULL,
    CONSTRAINT [PK_Career] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Category]
(
    [Id] UNIQUEIDENTIFIER NOT NULL,   
    [Title] NVARCHAR(160) NOT NULL,
    [Url] NVARCHAR(1024) NOT NULL,
    [Summary] NVARCHAR(2000) NOT NULL,
    [Order] INT NOT NULL,
    [Description] TEXT NOT NULL,
    [Featured] BIT NOT NULL,
    CONSTRAINT [PK_Category] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Course]
(
    [Id] UNIQUEIDENTIFIER NOT NULL,   
    [Tag] NVARCHAR(20) NOT NULL,
    [Title] NVARCHAR(160) NOT NULL,
    [Summary] NVARCHAR(2000) NOT NULL,
    [Url] NVARCHAR(1024) NOT NULL,
    [Level] TINYINT NOT NULL,
    [DurationInMinutes] INT NOT NULL,
    [CreateDate] DATETIME NOT NULL,
    [LastUpdateDate] DATETIME NOT NULL,
    [Active] BIT NOT NULL,
    [Free] BIT NOT NULL,
    [Featured] BIT NOT NULL,
    [AuthorId] UNIQUEIDENTIFIER NOT NULL,
    [CategoryId] UNIQUEIDENTIFIER NOT NULL,
    [Tags] NVARCHAR(160) NOT NULL,
    CONSTRAINT [PK_Course] PRIMARY KEY ([Id]),
    CONSTRAINT [PK_Course_Author_AuthorId] FOREIGN KEY ([AuthorId]) REFERENCES [Author] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [PK_Course_Category_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [Category] ([Id]) ON DELETE NO ACTION
);
GO

CREATE TABLE [CareerItem]
(
    [CareerId] UNIQUEIDENTIFIER NOT NULL,
    [CourseId] UNIQUEIDENTIFIER NOT NULL,
    [Titlee] NVARCHAR(160) NOT NULL,
    [Description] TEXT NOT NULL,
    [Order] TINYINT NOT NULL,    
    CONSTRAINT [PK_CareerItem] PRIMARY KEY ([CourseId], [CareerId]),
    CONSTRAINT [FK_CareerItem_Career_CareerId] FOREIGN KEY ([CareerId]) REFERENCES [Career] ([Id]),
    CONSTRAINT [FK_CareerItem_Course_CourseId] FOREIGN KEY ([CourseId]) REFERENCES [Course] ([Id])
);
GO

CREATE TABLE [StudentCourse]
(
    [CourseId] UNIQUEIDENTIFIER NOT NULL,    
    [StudentId] UNIQUEIDENTIFIER NOT NULL,
    [Progress] TINYINT NOT NULL,
    [Favorite] BIT NOT NULL,
    [StartDate] DATETIME NOT NULL,
    [LastUpdateDate] DATETIME NULL DEFAULT(GETDATE()),
    CONSTRAINT [PK_StudentCourse] PRIMARY KEY ([CourseId], [StudentId]),
    CONSTRAINT [FK_StudentCourse_Course_CourseId] FOREIGN KEY ([CourseId]) REFERENCES [Course] ([Id]),
    CONSTRAINT [FK_StudentCourse_Student_StudentId] FOREIGN KEY ([StudentId]) REFERENCES [Student] ([Id])
)

SELECT GETDATE()
SELECT GETUTCDATE()

View vw_Courses
CREATE OR ALTER VIEW [vw_Courses] AS
    SELECT
        [Course].[Id],
        [Course].[Tag],
        [Course].[Title],
        [Course].[Url],
        [Course].[Summary],
        [Course].[CreateDate],
        [Category].[Title] AS [Category],
        [Author].[Name] AS [Author]
    FROM    
        [Course]
    INNER JOIN 
        [Category]
    ON 
        [Course].[CategoryId] = [Category].[Id]
    INNER JOIN 
        [Author]
    ON 
        [Course].[AuthorId] = [Author].[Id]
    WHERE 
        [Active] = 1

SELECT * FROM [vw_Courses] 
ORDER BY [CreateDate]

View vw_Careers
CREATE OR ALTER VIEW [vw_Careers] AS
SELECT 
    [Career].[Id],
    [Career].[Title],
    [Career].[Url],
    COUNT([Id]) AS [Courses]
FROM
    [Career]
INNER JOIN 
    [CareerItem]
ON  
    [Career].[Id] = [CareerItem].[CareerId]
GROUP BY
    [Career].[Id],
    [Career].[Title],
    [Career].[Url]

SELECT * FROM [vw_Careers]

Inserindo registro em Student e StudentCourse
INSERT INTO 
    [Student] 
Values
(
    '601b0f1b-8feb-499c-aaf7-f14f14fd5014',
    'Douglas André',
    'dglandre@gmail.com',
    '12345678901',
    '11953813315',
    '1987-04-07',
    GETDATE()
)

INSERT INTO 
    [StudentCourse]
VALUES
(
    '5f5a33f8-4ff3-7e10-cc6e-3fa000000000',
    '601b0f1b-8feb-499c-aaf7-f14f14fd5014',
    50,
    0,
    '2020-01-13 12:35:54',
    GETDATE()
)

Visualizando o Progresso de Student
DECLARE @StudentId UNIQUEIDENTIFIER = '601b0f1b-8feb-499c-aaf7-f14f14fd5014'

SELECT
    [Student].[Name] AS [Student],
    [Course].[Title] AS [Course],
    [StudentCourse].[Progress],
    [StudentCourse].[LastUpdateDate]
FROM
    [StudentCourse]
INNER JOIN
    [Student]
ON
    [StudentCourse].[StudentId] = [Student].[Id]
INNER JOIN
    [Course]
ON
    [StudentCourse].[CourseId] = [Course].[Id]
WHERE
    [StudentCourse].[StudentId] = @StudentId
    AND [StudentCourse].[Progress] < 100
    AND [StudentCourse].[Progress] > 0
ORDER BY    
    [StudentCourse].[LastUpdateDate] DESC

Listando Cursos e Progressos
SELECT
    [Course].[Title] AS [Course],
    [Student].[Name] AS [Student],
    [StudentCourse].[Progress],
    [StudentCourse].[LastUpdateDate]
FROM
    [Course]
LEFT JOIN
    [StudentCourse]
ON
    [StudentCourse].[CourseId] = [Course].[Id]
LEFT JOIN
    [Student]
ON
    [StudentCourse].[StudentId] = [Student].[Id]
	
PROCEDURE
CREATE OR ALTER PROCEDURE [sp_StudentProgress] (@StudentId UNIQUEIDENTIFIER) AS
    SELECT
        [Student].[Name] AS [Student],
        [Course].[Title] AS [Course],
        [StudentCourse].[Progress],
        [StudentCourse].[LastUpdateDate]
    FROM
        [StudentCourse]
    INNER JOIN
        [Student]
    ON
        [StudentCourse].[StudentId] = [Student].[Id]
    INNER JOIN
        [Course]
    ON
        [StudentCourse].[CourseId] = [Course].[Id]
    WHERE
        [StudentCourse].[StudentId] = @StudentId
        AND [StudentCourse].[Progress] < 100
        AND [StudentCourse].[Progress] > 0
    ORDER BY    
        [StudentCourse].[LastUpdateDate] DESC

EXEC [sp_StudentProgress] '601b0f1b-8feb-499c-aaf7-f14f14fd5014'

--PROCEDURE - Excluir Extudent
CREATE OR ALTER PROCEDURE [sp_DeleteStudent] (@StudentId UNIQUEIDENTIFIER) AS

BEGIN TRANSACTION
    DELETE FROM 
        [StudentCourse] 
    WHERE 
        [StudentId] = @StudentId
    DELETE FROM 
        [Student] 
    WHERE 
        [Id] = @StudentId
COMMIT

EXEC [sp_DeleteStudent] '601b0f1b-8feb-499c-aaf7-f14f14fd5014'
![image](https://user-images.githubusercontent.com/58392536/213589821-6f19b85e-7c0d-44ef-a406-b375fce99c16.png)
