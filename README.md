
# Web Scraping da Tabela do Campeonato Brasileiro

Este projeto utiliza **Selenium** e **BeautifulSoup** para extrair informações da tabela do Campeonato Brasileiro de Futebol. O objetivo é coletar dados sobre as equipes, suas posições, estatísticas de jogos e resultados de partidas recentes.

## Observação 

Estamos ultilizando O Selenium para acessar pagina do g1 por conta que BeautifulSoup não
consgue lidar muito bem com pagina que rederizam Js, assim temos que utiliza Selenium para baixar os arquivos html da pagina.
## Tecnologias Utilizadas

- [Python](https://www.python.org/)
- [Selenium](https://www.selenium.dev/)
- [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Tabulate](https://pypi.org/project/tabulate/)
- [webdriver-manager](https://github.com/SergeyPirogov/webdriver_manager)
- [JupyterNotebook](https://github.com/jupyter/notebook)

## Pré-requisitos

Antes de executar o projeto, você precisa ter o Python e as seguintes bibliotecas instaladas. Você pode instalá-las usando o `pip`:

## Configuração do Ambiente

1. **Criar um ambiente virtual**:

   Para isolar as dependências do projeto, é recomendado criar um ambiente virtual. No terminal, navegue até o diretório do seu projeto e execute:

   ```bash
   python -m venv venv

2. **Ativar o ambiente**

    ```bash
    .\venv\Scripts\activate.bat

3. **Instalar o arquivo requirements.txt**

    ```bash
    pip install -r requirements.txt
 
## Importando as bibliotecas

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options  
from webdriver_manager.chrome import ChromeDriverManager  
from selenium.webdriver.common.by import By


from bs4 import BeautifulSoup
import time
```
## Configurando selenium
```python
chrome_options = Options()
chrome_options.add_argument("--headless") 
service = Service(ChromeDriverManager().install())
driver = webdriver.Chrome(service=service, options=chrome_options)
```

## Passa ULR do site que vai acessar:

```python

url = 'https://ge.globo.com/futebol/brasileirao-serie-b/'
driver.get(url)
html = driver.page_source
driver.quit()
```

## Chama BeautifulSoup

```
soup = BeautifulSoup(html, 'html.parser')
```

## PROCESSO DE EXTRAÇÃO
1. Após rodar os codigos acima temos que validar os dados que queremos extrair do site que vamos analisar
![image](https://github.com/user-attachments/assets/fa7200e2-6f97-494b-bf0e-9d06cb0026a1)

## Onde estão os dados?

2. Os dados se encotram dentro de uma " section " e pertecem a class tabela "tabela__pontos-corridos"

vamos procurar todas a section que tem o nome "tabela tabela__pontos-corridos"

```
sections = soup.find_all('section', class_="tabela tabela__pontos-corridos")

for i, section in enumerate(sections):
    print(f"Seção {i+1}:")
    print(section.get_text(strip=True))  
    print("-" * 40)  
```

3. Como podemos ver na imagem a cima os dados se encotram dentro de uma tabela e depois  outra tabela e desse modo precisamos entrar dentro dela para extrarir as informações

o codigo abaixo detalha esse processo de extração, veja que estou usando as tags do html para percorer essa tabela

```
sections = soup.find_all('section',class_="tabela tabela__pontos-corridos")
for i, section in enumerate(sections):
    tables = section.find_all('table', class_="tabela__equipes tabela__equipes--com-borda")  
    if tables:
        print(f"Seção {i+1} contém {len(tables)} tabela(s):")
        for j, table in enumerate(tables):
            print(f"Tabela {j+1}:")
            rows = table.find_all('tr')  

            for row in rows:
                cols = row.find_all(['td', 'th'])  # Acessar células da tabela
                cols_text = [col.get_text(strip=True) for col in cols]  
                print(cols_text)  
            print("-" * 40)  
```

A saída sera um texto com varios itens:
Seção 1 contém 1 tabela(s):
Tabela 1:
```
----------------------------------------
['Classificação']
['1', 'SantosSAN', '1']
['2', 'NovorizontinoNOV', '1']
['3', 'SportSPT', '0']
['4', 'MirassolMIR', '0']
['5', 'CearáCEA', '2']
['6', 'América-MGAME', '1']
['7', 'Vila NovaVNO', '1']
['8', 'Operário-PROPE', '0']
['9', 'AvaíAVA', '0']
['10', 'AmazonasAMA', '0']
['11', 'CoritibaCFC', '1']
['12', 'GoiásGOI', '1']
['13', 'PaysanduPAY', '4']
['14', 'Botafogo-SPBSP', '0']
['15', 'Ponte PretaPON', '2']
['16', 'ChapecoenseCHA', '1']
['17', 'CRBCRB', '1']
['18', 'ItuanoITU', '0']
['19', 'BrusqueBRU', '0']
['20', 'GuaraniGUA', '0']
----------------------------------------
```

Após isso precisamos buscar o restante dos dados que se encotram dentro de outra tabela:

```
sections = soup.find_all('section', class_="tabela tabela__pontos-corridos")
for i, section in enumerate(sections):
    tables = section.find_all('table',class_="tabela__pontos")  
    if tables:
        print(f"Seção {i+1} contém {len(tables)} tabela(s):")
        for j, table in enumerate(tables):
            print(f"Tabela {j+1}:")
            rows = table.find_all('tr')  

            for row in rows:
                cols = row.find_all(['td', 'th'])  
                cols_text = [col.get_text(strip=True) for col in cols]  
                print(cols_text)  
            print("-" * 40)  

```

O resultado terá a seguintes saida:

```
----------------------------------------
Tabela 1:
['P', 'J', 'V', 'E', 'D', 'GP', 'GC', 'SG', '%', 'ÚLT. JOGOS']
['60', '29', '18', '6', '5', '47', '25', '22', '68', '']
['57', '29', '17', '6', '6', '46', '20', '26', '65', '']
['55', '29', '16', '7', '6', '38', '29', '9', '63', '']
['51', '28', '15', '6', '7', '45', '32', '13', '60', '']
['47', '29', '14', '5', '10', '38', '32', '6', '54', '']
['46', '28', '12', '10', '6', '37', '26', '11', '54', '']
['45', '29', '13', '6', '10', '39', '33', '6', '51', '']
['43', '29', '12', '7', '10', '35', '29', '6', '49', '']
['40', '28', '10', '10', '8', '40', '41', '-1', '47', '']
['37', '28', '10', '7', '11', '32', '38', '-6', '44', '']
['35', '29', '10', '5', '14', '33', '37', '-4', '40', '']
['35', '29', '9', '8', '12', '36', '42', '-6', '40', '']
['34', '29', '8', '10', '11', '34', '38', '-4', '39', '']
['34', '29', '8', '10', '11', '33', '39', '-6', '39', '']
['31', '27', '8', '7', '12', '27', '31', '-4', '38', '']
['30', '28', '8', '6', '14', '22', '30', '-8', '35', '']
['29', '29', '8', '5', '16', '32', '44', '-12', '33', '']
['29', '29', '6', '11', '12', '29', '38', '-9', '33', '']
['26', '28', '6', '8', '14', '25', '39', '-14', '30', '']
['21', '29', '5', '6', '18', '22', '47', '-25', '24', '']
----------------------------------------
```

Agora precisamos juntar todos os dados 
```
import re
from tabulate import tabulate

sections = soup.find_all('section', class_="tabela tabela__pontos-corridos")

tabela_completa = []

cabecalho = ['Posição', 'Time', 'P', 'J', 'V', 'E', 'D', 'GP', 'GC', 'SG', '%', 'ÚLT. JOGOS']
tabela_completa.append(cabecalho)

for section in sections:
    tabela_equipes = section.find_all('table', class_="tabela__equipes tabela__equipes--com-borda")
    
    # 2. Encontrar a tabela de pontos (estatísticas: jogos, vitórias, derrotas, etc.)
    tabela_pontos = section.find_all('table', class_="tabela__pontos")
    
    if tabela_equipes and tabela_pontos:  # Se ambas as tabelas existirem
        # 3. Iterar sobre as tabelas de equipes e pontos em paralelo
        for table_equipes, table_pontos in zip(tabela_equipes, tabela_pontos):
            rows_equipes = table_equipes.find_all('tr')
            rows_pontos = table_pontos.find_all('tr')
            
             # Combinar as informações linha a linha
            for row_equipe, row_ponto in zip(rows_equipes, rows_pontos):
                # Extrair as informações das equipes (nome do time, posição)
                cols_equipe = row_equipe.find_all(['td', 'th'])
                cols_text_equipe = [col.get_text(strip=True) for col in cols_equipe]

                # Remover sufixos dos nomes dos times
                if len(cols_text_equipe) > 1:
                    nome_time = cols_text_equipe[1].replace('BOT', '').replace('PAL', '').replace('FOR', '').replace('FLA', '').replace('SAO', '').replace('INT', '').replace('BAH', '').replace('CRU', '').replace('CAM', '').replace('VAS', '').replace('GRE', '').replace('CRI', '').replace('RBB', '').replace('JUV', '').replace('CAP', '').replace('FLU', '').replace('VIT', '').replace('COR', '').replace('CUI', '').replace('ACG', '').strip()
                    cols_text_equipe[1] = nome_time

                cols_ponto = row_ponto.find_all(['td', 'th'])
                cols_text_ponto = [col.get_text(strip=True) for col in cols_ponto]

                ultimos_jogos_col = row_ponto.find_all('td', class_="classificacao__pontos classificacao__pontos--ultimos_jogos")
                ultimos_jogos_resultados = []
                
                for col in ultimos_jogos_col:
                    spans = col.find_all('span')
                    for span in spans:
                        span_class = span.get('class', [])
                        
                        # Mapeando vitórias, empates e derrotas
                        if "classificacao__ultimos_jogos--v" in span_class:
                            ultimos_jogos_resultados.append('V')  # Vitória
                        elif "classificacao__ultimos_jogos--e" in span_class:
                            ultimos_jogos_resultados.append('E')  # Empate
                        elif "classificacao__ultimos_jogos--d" in span_class:
                            ultimos_jogos_resultados.append('D')  # Derrota

                linha_completa = cols_text_equipe + cols_text_ponto
                
                if linha_completa[-1] == '':
                    linha_completa.pop()  

                # Insira os resultados dos últimos jogos na posição correta
                if ultimos_jogos_resultados:  
                    linha_completa.append(' '.join(ultimos_jogos_resultados))  
                else:
                    linha_completa.append('')  

                if linha_completa[0] != "Classificação":  
                    tabela_completa.append(linha_completa)

print(tabulate(tabela_completa, headers='firstrow', tablefmt='grid'))
```

A saida do codigo irá resultar em tabela dos dados organizados:
```
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|    | Posição       |   Time |   P |   J |   V |   E |   D |   GP |   GC |   SG |   % | ÚLT. JOGOS   |
+====+===============+========+=====+=====+=====+=====+=====+======+======+======+=====+==============+
|  1 | Botafogo      |      0 |  60 |  29 |  18 |   6 |   5 |   47 |   25 |   22 |  68 | V V V E V    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  2 | Palmeiras     |      0 |  57 |  29 |  17 |   6 |   6 |   46 |   20 |   26 |  65 | V V V V E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  3 | Fortaleza     |      0 |  55 |  29 |  16 |   7 |   6 |   38 |   29 |    9 |  63 | D E V V D    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  4 | Flamengo      |      0 |  51 |  28 |  15 |   6 |   7 |   45 |   32 |   13 |  60 | D E D V V    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  5 | São Paulo     |      0 |  47 |  29 |  14 |   5 |  10 |   38 |   32 |    6 |  54 | D V D V D    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  6 | Internacional |      1 |  46 |  28 |  12 |  10 |   6 |   37 |   26 |   11 |  54 | V V V V E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  7 | Bahia         |      1 |  45 |  29 |  13 |   6 |  10 |   39 |   33 |    6 |  51 | D V D V D    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  8 | Cruzeiro      |      0 |  43 |  29 |  12 |   7 |  10 |   35 |   29 |    6 |  49 | V D E E D    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
|  9 | Atlético-MG   |      0 |  40 |  28 |  10 |  10 |   8 |   40 |   41 |   -1 |  47 | V D V D E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 10 | Vasco         |      0 |  37 |  28 |  10 |   7 |  11 |   32 |   38 |   -6 |  44 | V E D E E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 11 | Grêmio        |      2 |  35 |  29 |  10 |   5 |  14 |   33 |   37 |   -4 |  40 | D E V E V    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 12 | Criciúma      |      2 |  35 |  29 |   9 |   8 |  12 |   36 |   42 |   -6 |  40 | D D E D V    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 13 | Bragantino    |      2 |  34 |  29 |   8 |  10 |  11 |   34 |   38 |   -4 |  39 | V E D E E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 14 | Juventude     |      2 |  34 |  29 |   8 |  10 |  11 |   33 |   39 |   -6 |  39 | D V D E E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 15 | Athletico-PR  |      0 |  31 |  27 |   8 |   7 |  12 |   27 |   31 |   -4 |  38 | D E E D D    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 16 | Fluminense    |      2 |  30 |  28 |   8 |   6 |  14 |   22 |   30 |   -8 |  35 | V D D D V    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 17 | Vitória       |      1 |  29 |  29 |   8 |   5 |  16 |   32 |   44 |  -12 |  33 | D V V D E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 18 | Corinthians   |      1 |  29 |  29 |   6 |  11 |  12 |   29 |   38 |   -9 |  33 | V D V D E    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 19 | Cuiabá        |      0 |  26 |  28 |   6 |   8 |  14 |   25 |   39 |  -14 |  30 | V D E D V    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
| 20 | Atlético-GO   |      0 |  21 |  29 |   5 |   6 |  18 |   22 |   47 |  -25 |  24 | D D D V D    |
+----+---------------+--------+-----+-----+-----+-----+-----+------+------+------+-----+--------------+
```

### Trabalhos futuros

Pra trabalhos futuros eu quero implemtar uma interface que permita o usuario mudar o Campeonato, alem de mandar essa tabela para o telegram pra testar outras funcionalidades.
