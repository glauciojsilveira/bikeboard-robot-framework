# Site e-comerce — Robot Framework | Test Continuos | Git Hub Action | MCP | Guide by Glaucio

## Descrição
Este projeto é uma plataforma de classificados para compra e venda de bicicletas, utilizando Robot Framework para testes automatizados, integração contínua com GitHub Actions e suporte ao Model Context Protocol (MCP) para extensões e automação.

## Pré-requisitos
- Node.js (versão 14 ou superior)
- Python (versão 3.8 ou superior)
- Git
- Navegador Chromium (instalado automaticamente pelo robotframework-browser)

## Instalação

### 1. Clonar o repositório
```bash
git clone <url-do-repositorio>
cd bikeboard-robot
```

### 2. Instalar dependências do Node.js
```bash
npm install
```

### 3. Instalar dependências do Python
```bash
pip install -r requirements.txt
 - Instalar o Robot Framework: pip install robotframework , pip install robotframework-browser , pip install -r requirements.txt , rfbrowser init ou python -m Browser.entry init, robot --version , robot -d ./logs robot/tests/
```

### 4. Inicializar o navegador para Robot Framework
```bash
rfbrowser init
```
Este comando instala o Chromium e configura o robotframework-browser.

### 5. Configurar MCP (opcional)
Se você usar extensões VS Code com MCP, o arquivo `.vscode/mcp.json` já está configurado para o servidor `robotframework-mcp`. Certifique-se de que o pacote `robotframework-mcp` esteja instalado:
```bash
pip install robotframework-mcp
```

## Configuração
- O projeto usa mocks JavaScript em `robot/resources/mocks/ads.js` para simular APIs durante os testes.
- Os testes estão localizados em `robot/tests/`.
- A aplicação web está em `src/`.

## Como rodar a aplicação
Para iniciar o servidor web localmente:
```bash
npm start
```
Isso iniciará o servidor em `http://localhost:3000` (ou porta padrão do `serve`).

## Como executar os testes
### Comando básico para executar todos os testes
```bash
robot robot/tests/
```

### Comando para executar um teste específico (ex: ads.robot)
```bash
robot robot/tests/ads.robot
```

### Com saída em diretório específico
```bash
robot --outputdir results robot/tests/ads.robot
```

### Com relatórios HTML
```bash
robot --outputdir results --log log.html --report report.html robot/tests/ads.robot
```

### Executar apenas testes com tags específicas
```bash
robot --include smoke robot/tests/
```

## Integração Contínua com GitHub Actions
Para configurar CI/CD, crie um arquivo `.github/workflows/ci.yml` no repositório com conteúdo similar a:
```yaml
name: E2E Testes Robot
on:
  workflow_dispatch:

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v5

    - name: Install Node
      uses: actions/setup-node@v4
      with:
        node-version: 22
        cache: 'npm'

    - name: Install dependencies WebApp
      run: npm install -g wait-on
      
    - name: Set up Python
      uses: actions/setup-python@v6
      with:
        python-version: '3.13'

    - name: Install dependencies Tests
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    
    - name: Browser install lib init
      run: rfbrowser init

    - name: Install Serve
      run: npm install -g serve

    - name: Run WeApp
      run: npm start & 
           sleep 15
      env:
        CI: true
        
    - name: Check if Port 3000 is active
      run: netstat -tulpn | grep 3000 || echo "Porta 3000 não está ativa ainda"

    - name: Wait for WebApp
      run: npx wait-on http://localhost:3000 --timeout 600000 --verbose

    - name: Run E2E Tests Robot Framework
      run: robot -d ./logs robot/tests

    - name: Publish Test Results
      uses: actions/upload-artifact@v4
      if: always()
      with:
        name: robot-test-results
        path: logs/
```

## Estrutura do Projeto
- `src/`: Código fonte da aplicação web
- `robot/tests/`: Arquivos de teste do Robot Framework
- `robot/resources/`: Recursos compartilhados (libraries, variables)
- `requirements.txt`: Dependências Python
- `package.json`: Dependências Node.js e scripts

## Comandos Úteis
- `npm start`: Inicia o servidor web
- `robot --help`: Ajuda do Robot Framework
- `rfbrowser init`: Inicializa navegadores para testes

## Contribuição
Siga as melhores práticas de desenvolvimento e execute os testes antes de enviar pull requests.

## Autor
Guide by Glaucio
