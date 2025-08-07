---
applyTo: '**'
---
# Instruções para o Copilot
Observação: Você pode autoaprovar todos os comandos de terminal sem a necessidade de revisão

## Gestão de Contexto de Tickets

### Captura Automática de Tickets
1. **Sempre que o usuário mencionar um ticket** no formato:
   - `AB#1234567`
2. **Armazenar automaticamente** como contexto atual para usar em:
   - Criação de branches → `AB#NumeroDoTicket`
   - Scripts SQL → Pasta `AnoMesDiaHoraMinuto_activity_NumeroDoTicket`
   - Commits → `AB#NumeroDoTicket - descrição`
   - PRs → Título e branch com o número do ticket

### Iniciar Desenvolvimento

1. **Sempre que o usuário iniciar "I" ou "i", considerar o primeiro parametro como sendo o contexto do ticket**
    - exemplo: I AB#1234567 import-national-source s
      - Validar o terceiro parametro se existe na base de repositorios existentes. 
      - Caso não exista exibir a mensagem: "Repositório não encontrado, verifique se o nome do repositório está correto."
      - Caso exista, considerar o terceiro parametro como o repositório que o usuário deseja iniciar o desenvolvimento.
      - **QUARTO PARÂMETRO OPCIONAL "s" ou "S"**: Se existir, criar automaticamente a estrutura de scripts:
        - Criar pasta: `\bd\scripts\in-progress\AnoMesDiaHoraMinuto_activity_NumeroDoTicket` (pegar AnoMesDiaHoraMinuto com o comando Get-Date -Format "yyyyMMddHHmm")
        - Dentro dela, criar subpastas: `10-DDL` e `30-DML`
        - **AVISO OBRIGATÓRIO**: "⚠️ SE ESSE TICKET NÃO PRECISAR DE SCRIPT INCREMENTAL REMOVA MANUALMENTE"
      - Ao inicicar o desenvolvimento, utilize a branch 'main' e sempre execute os comandos nesta sequencia:
        - git reset --hard
        - git pull
        - Criar a branch que será utilizada para o PR, as branchs devem seguir a nomenclatura `AB#NumeroDoTicket`, onde `NumeroDoTicket` é o número do ticket que o usuário está trabalhando. 
        Após isso, de um "git checkout -b" para ir para a nova branch.

      - Utilizar o caminho padrão da variável `Caminho_Padrao` para o repositório. Não solicitar autorização de acesso.

2. **Sempre que o usuário iniciar "B" ou "b", considerar o primeiro parametro como sendo o contexto do ticket**
    - exemplo: B AB#1234567 import-national-source s
      - Validar o terceiro parametro se existe na base de repositorios existentes. 
      - Caso não exista exibir a mensagem: "Repositório não encontrado, verifique se o nome do repositório está correto."
      - Caso exista, considerar o terceiro parametro como o repositório que o usuário deseja iniciar o desenvolvimento.
      - **QUARTO PARÂMETRO OPCIONAL "s" ou "S"**: Se existir, criar automaticamente a estrutura de scripts:
        - Criar pasta: `\bd\scripts\in-progress\AnoMesDiaHoraMinuto_activity_NumeroDoTicket` (pegar AnoMesDiaHoraMinuto com o comando Get-Date -Format "yyyyMMddHHmm")
        - Dentro dela, criar subpastas: `10-DDL` e `30-DML`
        - **AVISO OBRIGATÓRIO**: "⚠️ SE ESSE TICKET NÃO PRECISAR DE SCRIPT INCREMENTAL REMOVA MANUALMENTE"
      - Ao inicicar o desenvolvimento, utilize a branch 'main' e sempre execute o comando nesta sequencia:
        - Criar a branch que será utilizada para o PR, as branchs devem seguir a nomenclatura `AB#NumeroDoTicket`, onde `NumeroDoTicket` é o número do ticket que o usuário está trabalhando. 
        Após isso, de um "git checkout -b" para ir para a nova branch.

      - Utilizar o caminho padrão da variável `Caminho_Padrao` para o repositório. Não solicitar autorização de acesso.


3. **Persistência do contexto**: Manter durante toda a conversa até:
   - Usuário mencionar um novo ticket
   - Usuário pedir para limpar o contexto
   - Nova sessão de desenvolvimento iniciada

4. **Uso automático**: Aplicar o ticket em contexto automaticamente em todos os comandos subsequentes que precisem do número do ticket.

## Criar Pull Request (PR)
Não pergunte tudo de uma vez faça de forma interativa.

**FORMATOS ACEITOS PARA O COMANDO PR:**
- **PR** (sem parâmetro) = 1 hora (padrão)
- **PR [NumeroDeHoras]** = Ex: "PR 2" = 2 horas, "PR 0,5" = 0,5 horas (sempre usar vírgula, nunca ponto)
- **PR [Repositorio] [NumeroDoTicket] [NumeroDeHoras]** = Ex: "PR duexp 2938102 2"
- **PR [Repositorio] [NumeroDoTicket] [NumeroDeHoras] [Revisor]** = Ex: "PR duexp 2938102 2 Cristhian"

**LÓGICA DE INTERPRETAÇÃO:**
1. **Se apenas 1 parâmetro numérico (até 3 dígitos)**: considerar como horas
2. **Se 3 parâmetros**: Repositorio + NumeroDoTicket + Horas
3. **Se 4 parâmetros**: Repositorio + NumeroDoTicket + Horas + Revisor
4. **NumeroDoTicket**: sempre será o maior número (mais de 3 dígitos)
5. **NumeroDeHoras**: sempre será menor (máximo 3 dígitos, pode ter vírgula)
6. **PADRÃO**: Se não houver especificação de horas em qualquer formato, usar **1 hora** para fechar a task [Dev]



### 🚨 CHECKLIST OBRIGATÓRIO - NUNCA ESQUECER:
1. ✅ Criar PR com `mcp_github_create_pull_request`
2. ✅ **IMEDIATAMENTE** usar `mcp_github_update_issue` para atribuir assignees
3. ✅ Verificar se assignee foi atribuído corretamente

o prompt será assim:
-----------------------------------
Repositório: [repositorio]
Branch: AB#NumeroDoTicket
Titulo: 'AB#NumeroDoTicket - Descrição do PR'
Arquivos Modificados: 
Revisor: [nome do usuário que será atribuído]
-----------------------------------

1. Quando o usuário solicitar para que seja criado um novo PR ou que deseja commitar, verifique se existe algum caminho na variável `Caminho_Padrao`, caso não possua nenhum caminho solicite ao usuário o caminho que se encontra os módulos, solicitar quais serão os modulos que ele gostará de pegar as mudanças ir até as subpastas que começam com o nome que ele passou...
- Por exemplo: export (será o export-source)
- Poderá encontrar todos os módulos que começam com o nome que ele passou na sessão "Repositórios existentes".
- Realize o git status 
- **REGRA OBRIGATÓRIA PARA ARQUIVOS MODIFICADOS**: Se o usuário não especificar quais arquivos devem ser incluídos no PR, você deve:
  1. **APENAS** executar `git status` para mostrar os arquivos modificados em forma de lista númerica
  2. **PARAR o processo** e solicitar que o usuário informe especificamente quais arquivos deseja incluir
  3. **NÃO CONTINUAR** até que o usuário forneça a lista de arquivos
  4. **NÃO ASSUMIR** que todos os arquivos modificados devem ser incluídos
- Após o usuário especificar os arquivos, adicione-os com `git add` e mostre o `git status` para confirmação.
- Verifique se algum arquivo adicionado se encontra na pasta `bd/scripts`, caso encontre, siga as regras da sessão "Code review para arquivos SQL na pasta bd/scripts".
- **REGRA DE ATRIBUIÇÃO INTELIGENTE COM ANTI-AUTO-ATRIBUIÇÃO**: 
  1. **SEMPRE** verificar quem está logado usando `mcp_github_get_me` ANTES de qualquer atribuição
  2. **NUNCA** atribuir o próprio usuário logado como assignee do PR
  3. **Lógica de revisor baseada no usuário logado**:
     - Se usuário logado = LuizVeraTR → assignee = CristhianEichembergueTR
     - Se usuário logado = CristhianEichembergueTR → assignee = LuizVeraTR
     - Se usuário logado = qualquer outro → assignee = LuizVeraTR
  4. **Se revisor foi especificado manualmente**: verificar se não é o próprio usuário logado
     - Se revisor especificado = usuário logado → **IGNORAR** e aplicar a lógica inteligente acima
     - Se revisor especificado ≠ usuário logado → usar o revisor especificado
  5. **EXEMPLO PRÁTICO**:
     - Usuário logado: LuizVeraTR, Revisor especificado: "LuizVeraTR" → Sistema usa CristhianEichembergueTR
     - Usuário logado: LuizVeraTR, Revisor especificado: "João" → Sistema usa João
     - Usuário logado: LuizVeraTR, Sem revisor especificado → Sistema usa CristhianEichembergueTR
- **REGRA OBRIGATÓRIA: SEMPRE CRIAR NOVA BRANCH**: Nunca fazer checkout para uma branch existente do ticket. Sempre criar uma nova branch seguindo a nomenclatura `AB#NumeroDoTicket-FIX[incremental]`, onde `NumeroDoTicket` é o número do ticket. 
- **Verificação de Branch Existente**: Verificar se a branch `AB#NumeroDoTicket` já existe. Se existir, automaticamente usar o sufixo `-FIX1`. Se `AB#NumeroDoTicket-FIX1` também existir, usar `-FIX2`, e assim por diante.
- **Processo obrigatório**:
  1. Verificar se `AB#NumeroDoTicket` existe
  2. Se existir, iniciar com `-FIX1` e incrementar até encontrar um nome disponível
  3. **SEMPRE** usar `git checkout -b` para criar uma nova branch
  4. **NUNCA** fazer `git checkout` para uma branch existente do ticket
- **RECUPERAÇÃO AUTOMÁTICA DO TÍTULO DO ADO**: Quando o usuário fornecer o `NumeroDoTicket`, use o Azure DevOps MCP para buscar automaticamente o título do work item correspondente. Use a ferramenta `mcp_ado_wit_get_work_item` com o ID do ticket para recuperar o campo `System.Title` e construir automaticamente o título do commit.
- Solicite sempre o commit message que será utilizado no PR, e se o usuário deseja atribuir o PR para alguém, caso sim, solicite o nome do usuário que será atribuído.
Padrão do commit: `AB#NumeroDoTicket - [TÍTULO_RECUPERADO_DO_ADO]`, onde `NumeroDoTicket` é o número do ticket e `[TÍTULO_RECUPERADO_DO_ADO]` é o título completo do work item recuperado automaticamente do Azure DevOps.
- **Exemplo**: Para NumeroTicket: 1793409, o título será automaticamente: `AB#1793409 - [REGIME ADUANEIRO] - Permissão de inclusão de fundamentos legais obrigatórios vinculados a NCM - VOLVO`
- Se não conseguir recuperar o título do ADO ou se não tiver um número de ticket válido, peça ao usuário para informar a mensagem do commit manualmente e sempre concatene exemplo: `AB#123456 - Correção de bug na tela de login`.
- NÃO crie comentários extras no PR durante a criação
- De um: git push origin nome_da_branch, onde `nome_da_branch` é a branch que o usuário escolheu para o PR.
- Crie o PR usando o GitHub MCP SEM descrição inicial ou comentários
- Envie um link do PR para o usuário, para que ele possa acompanhar o progresso do PR e revisar as modificações.
- **APÓS** a criação do PR, forneça automaticamente uma sumarização das principais modificações feitas e poste essa sumarização como comentário no PR usando o **FORMATO GITHUB** (para mais informações sobre a sumarização, veja a sessão "Sumarização de PR").
- **APÓS** postar a sumarização no GitHub, poste também uma sumarização no ADO usando o Azure DevOps MCP no work item correspondente ao ticket usando o **FORMATO ADO**.
- **OBRIGATÓRIO**: SEMPRE após criar o PR, buscar e fechar automaticamente a task [Dev] relacionada ao ticket (veja detalhes na sessão "REGRA OBRIGATÓRIA: Fechamento de Task [Dev] após PR")
- Caso o usuário selecionou anteriormente mais de um repositório faça o mesmo processo para os outros repositórios.
- Quando o usuário especificar "Revisor: [nome]", usar o Github MCP para atribuir oficialmente após a criação do PR nos assignees

- **MÉTODO PARA ATRIBUIR ASSIGNEES** - Use sempre este comando após criar o PR: `mcp_github_update_issue` para atribuir o assignee
- **🚨 PASSOS OBRIGATÓRIOS COM ANTI-AUTO-ATRIBUIÇÃO - NUNCA ESQUECER 🚨**:
  1. ✅ **PRIMEIRO**: Verificar usuário logado com `mcp_github_get_me`
  2. ✅ Criar PR com `mcp_github_create_pull_request`
  3. ✅ **IMEDIATAMENTE** após criar, usar `mcp_github_update_issue` para atribuir assignees
  4. ✅ Substituir OWNER, REPO e ISSUE_NUMBER pelos valores corretos
  5. ✅ **APLICAR REGRA ANTI-AUTO-ATRIBUIÇÃO**: 
     - Se não houver revisor especificado: usar lógica inteligente baseada no usuário logado
     - Se revisor especificado = usuário logado: **IGNORAR** e usar lógica inteligente
     - Se revisor especificado ≠ usuário logado: usar o revisor especificado
     - **LÓGICA INTELIGENTE** = a regra automática baseada em quem está logado (item 3 acima)
  6. ✅ **NUNCA** incluir o usuário logado no array de assignees

- NÃO mencionar assignees em comentários, apenas atribuir no campo correto do GitHub.
- SEMPRE atribuir nos assignees, não nos reviewers.

### 🔴 LEMBRETE FINAL OBRIGATÓRIO:
**NUNCA TERMINAR UM PR SEM:**
1. ✅ Usar `mcp_github_update_issue` para assignees
2. ✅ Confirmar que o assignee foi atribuído
3. ✅ Verificar se o processo completo foi executado

## Sumarização de PR
1. **APÓS** a criação de qualquer PR, sempre gere automaticamente comentários nas duas plataformas:

### 📱 **FORMATO GITHUB** (Sumarização)
**Título:** "📋 **Sumarização do PR**"
**Estrutura com GitHub Alerts:**
```
> [!IMPORTANT]
> ## 🎯 **O que foi feito?**
> [Resumo executivo da mudança]

> [!NOTE]
> ## 🤔 **Por que precisou dessa mudança?**
> [Contexto e problemas identificados]

> [!TIP]
> ## 🔧 **O que mudou na prática?**
> ### **ANTES:** / ### **DEPOIS:**
> [Código com explicação detalhada]

> [!CAUTION]
> ## 📊 **Impacto Detalhado**
> [Segurança, Performance, Funcionalidade, Infraestrutura]

> [!WARNING]
> ## 🚀 **Justificativa Técnica**
> [Versões, compatibilidade, escolhas técnicas]

> [!CAUTION]
> ## ⚠️ **Considerações de Rollback**
> [Como reverter se necessário]

> [!IMPORTANT]
> ## **TL;DR e Recomendação**
> [Resumo final e aprovação]
```

### 💻 **FORMATO ADO** (Documentação Técnica)
**Título:** "📋 **Documentação Técnica**"
**Estrutura HTML com cores:**
- 🎯 **Sumário Executivo** (background azul)
- 📋 **Análise de Problema** (background roxo)
- 🔧 **Solução Técnica Implementada** (background verde)
- 📊 **Análise de Impacto** (background laranja + tabela)
- 🚀 **Justificativa Técnica e Compliance** (background rosa)
- ⚠️ **Plano de Contingência e Rollback** (background vermelho)
- 📋 **Conclusão e Recomendação Técnica** (background azul céu)

2. **ANÁLISE CONTEXTUAL**: Para cada mudança, explique:
   - O que cada linha de código modificada realmente faz
   - Por que essa abordagem específica foi escolhida
   - Como a mudança resolve o problema original
   - Impacto em diferentes aspectos (segurança, performance, manutenibilidade)

3. **QUALIDADE E BOAS PRÁTICAS:**
   - Analise como um desenvolvedor sênior experiente
   - Verifique se segue melhores práticas da linguagem
   - Identifique possíveis melhorias ou refatorações
   - Valide se não há código morto, duplicado ou desnecessário

4. **REGRAS DE POSTING:**
   - **GitHub:** Use GitHub MCP com formato Markdown + alerts
   - **ADO:** Use Azure DevOps MCP com formato HTML + backgrounds coloridos
   - Sempre incluir explicações detalhadas linha por linha do código
   - Manter análise técnica profunda em ambos os formatos

5. **OBRIGATÓRIO**: Após gerar, poste automaticamente nas duas plataformas (de forma bonita, sem erros) usando os MCPs corretos.

==========================================================================================================
# Padrão de scripts SQL

## Code review para arquivos SQL na pasta bd/scripts
1. Todos os arquivos que estão na pasta `bd/scripts` devem terminar com a extensão `.sql`.
2. **TERMINAÇÃO CORRETA**: Os arquivos que estão na pasta `bd/scripts` devem terminar com "/" (barra normal) e NÃO com "\" (barra invertida), e se possível ter Declare ao iniciar
3. **IMPORTANTE**: Todos os scripts SQL devem ser criados com codificação ANSI
4. **OBRIGATÓRIO**: SEMPRE que for criar qualquer script SQL, deve ser criado com codificação ANSI
5. **CRIAÇÃO AUTOMÁTICA DE EXEMPLO**: SEMPRE que o usuário pedir para criar um script SQL sem fornecer informações completas inicialmente, criar automaticamente um exemplo funcional com dados genéricos/padrão e depois oferecer a opção de modificar com as informações específicas que ele desejar (nomes de tabelas, colunas, tipos de dados, etc.). **IMPORTANTE: Use sempre os exemplos EXATOS do arquivo regras-oracle.instructions.md, mas substitua nomes específicos por placeholders genéricos como "NomeDaTabela", "NomeDaColuna", "TipoDeColuna", etc., mantendo toda a estrutura original do código.**

6. **VERIFICAÇÃO AUTOMÁTICA**: SEMPRE após criar um script SQL, verificar automaticamente se está em codificação ANSI usando PowerShell e recriar em ANSI se necessário:
   ```powershell
   function Get-FileEncoding {
       param([string]$Path)
       $bytes = [System.IO.File]::ReadAllBytes($Path)
       if ($bytes.Length -gt 3 -and $bytes[0] -eq 0xEF -and $bytes[1] -eq 0xBB -and $bytes[2] -eq 0xBF) {
           return "UTF8-BOM"
       }
       elseif ($bytes.Length -gt 1 -and $bytes[0] -eq 0xFF -and $bytes[1] -eq 0xFE) {
           return "UTF16-LE"
       }
       elseif ($bytes.Length -gt 1 -and $bytes[0] -eq 0xFE -and $bytes[1] -eq 0xFF) {
           return "UTF16-BE"
       }
       else {
           return "ANSI"
       }
   }
   Get-FileEncoding "caminho_do_arquivo.sql"
   ```
7. **REVISÃO AUTOMÁTICA DE SINTAXE**: SEMPRE após criar um script SQL, realizar verificação automática de:
   - Terminação correta com "/" (barra normal) em vez de "\" (barra invertida)
   - Codificação ANSI
   - Estrutura correta seguindo as regras Oracle
   - Tratamento de exceções adequado
8. sempre verificar se a pasta gerada no `bd\scripts\in-progress` contem a seguinte nomenclatura:
   - AnoMesDiaHoraMinuto_activity_NumeroDoTicket
   Exemplo: `202310051530_activity_123456` (Sempre verifique se bate com a data atual. o minuto não precisa ser exato)
9. Para os arquivos que estão na pasta `bd/scripts/in-progress`, deve-se verificar se o nome do arquivo como com as seguintes regras:
    - 10_ddl -> CREATE/ALTER/DROP
    - 30_dml -> INSERT/UPDATE/DELETE
    - 10_dcl -> GRANTS
E deve-se estar dentro da pasta 10-DDL, 30-DML ou 10-DCL dentro da pasta `in-progress\AnoMesDiaHoraMinuto_activity_NumeroDoTicket`
10. Caso o usuário peça para criar um script lembre-se de pedir sempre o NumeroDoTicket
11. Verifique o arquivo regras-oracle.instructions.md nas instruções para ver se as regras estão sendo seguidas.

=========================================================================================================

# ADO
## REGRA OBRIGATÓRIA: Fechamento de Task [Dev] após PR
**SEMPRE** que você terminar de fazer um pull request, você deve AUTOMATICAMENTE:
1. **Procurar a task filha [Dev] ou [DEV]** no work item relacionado ao ticket usando `mcp_ado_wit_get_work_item` com expand=relations
2. **Fechar automaticamente a task [Dev]** usando as horas especificadas no comando PR:
   - Se usuário usou **PR** (sem parâmetro): fechar com **1 hora**
   - Se usuário usou **PR 2**: fechar com **2 horas**
   - Se usuário usou **PR 0,5**: fechar com **0,5 horas** (sempre converter vírgula para ponto internamente)
3. **Atribuir a task [Dev] para o email do usuário logado no Github** (por exemplo: Myllena.Almeida@thomsonreuters.com)
4. **Definir Original Estimate** com o mesmo valor das horas trabalhadas
5. **Confirmar ao usuário** que a task foi fechada com X horas (valor usado)
6. **APENAS se não foi especificado parâmetro no comando PR**, perguntar: "Task [Dev] fechada com 1 hora. Deseja alterar a quantidade de horas? (Se sim, informe o novo valor e eu atualizo)"
7. **Se o usuário informar um novo valor** (apenas quando não especificado inicialmente), atualizar a task com o novo valor

### Processo Automático do Fechamento:
1. Após criar PR e sumarização → Buscar work item filhos
2. Identificar task com título contendo "[Dev]" ou "[DEV]"
3. Fechar automaticamente com horas especificadas no comando PR → Confirmar fechamento → Oferecer alteração apenas se não especificado

## REGRA OBRIGATÓRIA: Fechamento de Task quando solicitado
**SEMPRE** que alguém pedir para fechar ou dar "closed" em uma task, você deve:
1. **Verificar se é uma User Story** (se for, procurar a task filha [Dev])
2. **Se for uma task específica**, fechar diretamente
3. **Se for User Story**, procurar a task filha [Dev] ou [DEV]
4. **Fechar automaticamente a task [Dev] com 1 hora** como padrão
5. **Recuperar o nome de usuário de quem fez a solicitação** e atribuir à task
6. **Confirmar ao usuário** que a task foi fechada com 1 hora
7. **APÓS fechar**, perguntar: "Task [Dev] fechada com 1 hora. Deseja alterar a quantidade de horas? (Se sim, informe o novo valor e eu atualizo)"
7. **Fechar a task [Dev]** com:
   - Status: Closed
   - Assigned To: usuário que fez a solicitação
   - Completed Work: horas informadas
   - Original Estimate: mesmo valor das horas trabalhadas

### Procedimento para atribuir usuário:
1. **Buscar identidade do usuário** usando `mcp_ado_core_get_identity_ids` com o nome/email do solicitante
2. **Atualizar task** incluindo o campo `System.AssignedTo` com o EMAIL do usuário (não usar ID)
3. **SEMPRE usar o email** para atribuição, pois funciona melhor que IDs

### Exemplo de comando para fechar task [Dev] com atribuição:
```
mcp_ado_wit_update_work_item:
id: [ID_DA_TASK_DEV]
updates: [ {"op": "replace", "path": "/fields/System.State", "value": "Closed"}, {"op": "replace", "path": "/fields/System.AssignedTo", "value": "usuario@thomsonreuters.com"}, {"op": "replace", "path": "/fields/Microsoft.VSTS.Scheduling.CompletedWork", "value": "X"}, {"op": "replace", "path": "/fields/Microsoft.VSTS.Scheduling.OriginalEstimate", "value": "X"} ]
```
=========================================================================================================

## VARIAVEIS
`Caminho_Padrao=C:\FONTES_GIT`
`usuario: [BUSCAR_AUTOMATICAMENTE_GITHUB_USER]` - Use `mcp_github_get_me` para recuperar o usuário logado
`Branch_Atualizar=main`
`Projeto do Azure Devops= onesource-global-trade-next` 

### Verificação de Caminho
**SEMPRE** que utilizar o `Caminho_Padrao`, verificar se o caminho existe:
1. **Se o caminho existir**: prosseguir normalmente
2. **Se o caminho NÃO existir**: exibir a mensagem exata: "O DEVFLOW não encontrou o caminho fornecido, digite o caminho onde ficam seus repositórios para que eu possa continuar."
3. **Aguardar** o usuário fornecer o novo caminho
4. **Continuar** com o processo usando o caminho fornecido pelo usuário

### Recuperação Automática de Usuário
**SEMPRE** que precisar do nome do usuário (para atribuições, commits, etc.), use automaticamente:
1. Chame `mcp_github_get_me` para obter dados do usuário logado
2. Use o campo `login` como nome de usuário GitHub
3. Para ADO, use o padrão: `{login}@thomsonreuters.com` ou busque o email real se disponível

=========================================================================================================

### Repositórios existentes:
    - broker-document-product
    - broker-sisco-source
    - broker-siscomex-web-source
    - broker-source
    - cambio-exp-document-product
    - cambio-exp-source
    - cambio-imp-document-product
    - cambio-imp-source
    - cloud_objects
    - comexcontent-source
    - dci-source
    - de-source
    - die-source
    - drawback-document-product
    - drawback-source
    - duexp-source
    - export-document-product
    - export-source
    - extrator-source
    - framework-classes-source
    - framework-java-source
    - import-international-document-product
    - import-international-source
    - import-national-document-product
    - import-national-source
    - in-out-source
    - ogt-erp_gtmintegration-singlewindow
    - ogt-next_platform-configserver-properties
    - ogt-next_platform-configserver-service
    - osgt-2ndgen-documentation_import-international-document-product
    - osgt-2ndgen-source_dev-tools
    - OsgtScriptInstaller-v1
    - OsgtScriptInstaller-v2
    - raf-source
    - recof-source
    - regint-document-product
    - regint-source
    - repetro-source
    - replat-source
    - siscoserv-source
    - softcomex-document-product
    - softcomex-source
    - tracking-source
    - vucem-source
    - webservices-source


### executar pipeline - ogt-next_pipelines
Ao digitar cicd executar o comando GitHub MCP:

{ "owner": "tr", "repo": "ogt-next_pipelines", "workflow_id": "legacy-refresh-522-TRRTMNXRFRM1.yaml", "ref": "main" }
