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

1. **Comandos de Inicialização de Desenvolvimento** - O primeiro parâmetro sempre será o contexto do ticket:
   
   **1.1. Comando "I" (Inicialização com Reset):**
   - Exemplo: `I AB#1234567 import-national-source s`
   - Comportamento: utiliza a branch 'main' e executa os comandos OTIMIZADOS:
     - **COMANDO ÚNICO**: `cd "[Caminho_Padrao]\NomeDoRepositorio"; git checkout main; git reset --hard; git pull; git checkout -b AB#NumeroDoTicket; git branch --show-current`
   
   **1.2. Comando "B" (Inicialização sem Reset):**
   - Exemplo: `B AB#1234567 import-national-source s`
   - Comportamento: utiliza a branch 'main' e executa OTIMIZADO:
     - **COMANDO ÚNICO**: `cd "[Caminho_Padrao]\NomeDoRepositorio"; git checkout main; git checkout -b AB#NumeroDoTicket; git branch --show-current`
   
   **1.3. Regras Comuns para Ambos os Comandos:**
   - **Terceiro Parâmetro (Repositório)**: Validar se existe na base de repositórios existentes
     - Se não existir: exibir "Repositório não encontrado, verifique se o nome do repositório está correto."
     - Se existir: considerar como o repositório de desenvolvimento
   - **AUTOCOMPLETAR INTELIGENTE DE REPOSITÓRIOS**: Se o usuário fornecer nome incompleto, completar automaticamente baseado na lista de repositórios existentes
   - **Quarto Parâmetro Opcional "s" ou "S"**: Se existir, criar automaticamente a estrutura de scripts:
     - Criar pasta: `\bd\scripts\in-progress\AnoMesDiaHoraMinuto_activity_NumeroDoTicket` (usar Get-Date -Format "yyyyMMddHHmm")
     - Dentro dela, criar subpastas: `10-DDL` e `30-DML`
     - **AVISO OBRIGATÓRIO**: "⚠️ SE ESSE TICKET NÃO PRECISAR DE SCRIPT INCREMENTAL REMOVA MANUALMENTE"
   - **Finalização**: Após a preparação da branch:
     - Validar se o repositório existe na lista de "Repositórios existentes" antes da navegação
     - Criar branch com nomenclatura `AB#NumeroDoTicket`
     - **Se a branch já existir ou houver erro**: usar comando único `git status; git branch` para confirmar qual branch está ativa

2. **Persistência do contexto**: Manter durante toda a conversa até:
   - Usuário mencionar um novo ticket
   - Usuário pedir para limpar o contexto
   - Nova sessão de desenvolvimento iniciada

3. **Uso automático**: Aplicar o ticket em contexto automaticamente em todos os comandos subsequentes que precisem do número do ticket.

## Criar Pull Request (PR)
Não pergunte tudo de uma vez faça de forma interativa.

**FORMATOS ACEITOS PARA O COMANDO PR:**
- **PR** (sem parâmetro) = 1 hora (padrão)
- **PR [NumeroDeHoras]** = Ex: "PR 2" = 2 horas, "PR 0,5" = 0,5 horas (sempre usar vírgula, nunca ponto)
- **PR [Repositorio] [NumeroDoTicket] [NumeroDeHoras]** = Ex: "PR duexp 2938102 2"
- **PR [Repositorio] [NumeroDoTicket] [NumeroDeHoras] [Assignee]** = Ex: "PR duexp 2938102 2 Cristhian"

**LÓGICA DE INTERPRETAÇÃO:**
1. **Se apenas 1 parâmetro numérico (até 3 dígitos)**: considerar como horas
2. **Se 3 parâmetros**: Repositorio + NumeroDoTicket + Horas
3. **Se 4 parâmetros**: Repositorio + NumeroDoTicket + Horas + Assignee
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
Assignee: [nome do usuário que será atribuído]
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
- Após o usuário especificar os arquivos, adicione-os com comandos git batch: `git add arquivo1 arquivo2 arquivo3; git status` para confirmação.
- Verifique se algum arquivo adicionado se encontra na pasta `bd/scripts`, caso encontre, siga as regras da sessão "Code review para arquivos SQL na pasta bd/scripts".
- **REGRA DE ATRIBUIÇÃO INTELIGENTE COM ANTI-AUTO-ATRIBUIÇÃO**: 
  1. **SEMPRE** verificar quem está logado usando `mcp_github_get_me` ANTES de qualquer atribuição
  2. **NUNCA** atribuir o próprio usuário logado como assignee do PR
  3. **VERIFICAÇÃO OBRIGATÓRIA DO CAMPO AREA NO ADO**: 
     - **ANTES** de aplicar qualquer lógica de Assignee, usar `mcp_ado_wit_get_work_item` para buscar o campo "System.AreaPath" ou "Area" do ticket
     - **Se o campo Area contém a palavra "Operacionais"**: usar LuizVeraTR como Assignee padrão (aplicar lógica antiga)
     - **Se o campo Area NÃO contém "Operacionais"**: perguntar ao usuário o nome do Assignee para usar em todos os assignees
  4. **Lógica de Assignee baseada no usuário logado (APENAS se Area contém "Operacionais")**:
     - Se usuário logado = LuizVeraTR → assignee = CristhianEichembergueTR
     - Se usuário logado = CristhianEichembergueTR → assignee = LuizVeraTR
     - Se usuário logado = qualquer outro → assignee = LuizVeraTR
  5. **Se Assignee foi especificado manualmente**: verificar se não é o próprio usuário logado
     - Se Assignee especificado = usuário logado → **IGNORAR** e aplicar a lógica inteligente acima
     - Se Assignee especificado ≠ usuário logado → usar o Assignee especificado
  6. **EXEMPLO PRÁTICO**:
     - Ticket com Area "Operacionais": Usuário logado: LuizVeraTR, Sem Assignee especificado → Sistema usa CristhianEichembergueTR
     - Ticket com Area "Desenvolvimento": Sistema pergunta: "Pra quem devo assinar (Assignee)?" → Usuário responde: "João" → Sistema usa João
     - Ticket com Area "Operacionais": Usuário logado: LuizVeraTR, Assignee especificado: "João" → Sistema usa João
- **REGRA OBRIGATÓRIA: SEMPRE CRIAR NOVA BRANCH**: Nunca fazer checkout para uma branch existente do ticket. Sempre criar uma nova branch seguindo a nomenclatura `AB#NumeroDoTicket-FIX[incremental]`, onde `NumeroDoTicket` é o número do ticket. 
- **Verificação de Branch Existente**: Verificar se a branch `AB#NumeroDoTicket` já existe. Se existir, automaticamente usar o sufixo `-FIX1`. Se `AB#NumeroDoTicket-FIX1` também existir, usar `-FIX2`, e assim por diante.
- **Processo obrigatório**:
  1. Verificar se `AB#NumeroDoTicket` existe
  2. Se existir, iniciar com `-FIX1` e incrementar até encontrar um nome disponível
  3. **SEMPRE** usar `git checkout -b` para criar uma nova branch
  4. **NUNCA** fazer `git checkout` para uma branch existente do ticket
- **RECUPERAÇÃO AUTOMÁTICA DO TÍTULO DO ADO**: Quando o usuário fornecer o `NumeroDoTicket`, use o Azure DevOps MCP para buscar automaticamente o título do work item correspondente. Use a ferramenta `mcp_ado_wit_get_work_item` com o ID do ticket para recuperar o campo `System.Title` e construir automaticamente o título do commit.
- **LIMPEZA AUTOMÁTICA DO TÍTULO**: Após recuperar o título do ADO, SEMPRE remover automaticamente:
  - Aspas simples (') no início e fim do título
  - Aspas duplas (") no início e fim do título  
  - Tanto aspas simples quanto duplas que estejam envolvendo todo o título
- Solicite sempre o commit message que será utilizado no PR, e se o usuário deseja atribuir o PR para alguém, caso sim, solicite o nome do usuário que será atribuído.
Padrão do commit: `AB#NumeroDoTicket - [TÍTULO_RECUPERADO_DO_ADO_SEM_ASPAS]`, onde `NumeroDoTicket` é o número do ticket e `[TÍTULO_RECUPERADO_DO_ADO_SEM_ASPAS]` é o título completo do work item recuperado automaticamente do Azure DevOps com aspas removidas.
- **Exemplo**: Para NumeroTicket: 1793409, o título será automaticamente: `AB#1793409 - [REGIME ADUANEIRO] - Permissão de inclusão de fundamentos legais obrigatórios vinculados a NCM - VOLVO`
- Se não conseguir recuperar o título do ADO ou se não tiver um número de ticket válido, peça ao usuário para informar a mensagem do commit manualmente e sempre concatene exemplo: `AB#123456 - Correção de bug na tela de login`.
- NÃO crie comentários extras no PR durante a criação
- **COMANDO GIT OTIMIZADO**: Use sempre comandos batch: `git commit -m "AB#NumeroDoTicket - mensagem"; git push origin nome_da_branch;` em um único comando MCP.
- Crie o PR usando o GitHub MCP SEM descrição inicial ou comentários
- Envie um link do PR para o usuário, para que ele possa acompanhar o progresso do PR e revisar as modificações.
- **APÓS** a criação do PR, forneça automaticamente uma sumarização das principais modificações feitas e poste essa sumarização como comentário no PR usando o **FORMATO GITHUB** (para mais informações sobre a sumarização, veja a sessão "Sumarização de PR").
- **APÓS** postar a sumarização no GitHub, poste também uma sumarização no ADO usando o Azure DevOps MCP no work item correspondente ao ticket usando o **FORMATO ADO**.
- **OBRIGATÓRIO**: SEMPRE após criar o PR, buscar e fechar automaticamente a task [Dev] relacionada ao ticket (veja detalhes na sessão "REGRA OBRIGATÓRIA: Fechamento de Task [Dev] após PR")
- Caso o usuário selecionou anteriormente mais de um repositório faça o mesmo processo para os outros repositórios.
- Quando o usuário especificar "Assignee: [nome]", usar o Github MCP para atribuir oficialmente após a criação do PR nos assignees

- **MÉTODO PARA ATRIBUIR ASSIGNEES** - Use sempre este comando após criar o PR: `mcp_github_update_issue` para atribuir o assignee
- **🚨 PASSOS OBRIGATÓRIOS COM ANTI-AUTO-ATRIBUIÇÃO - NUNCA ESQUECER 🚨**:
  1. ✅ **PRIMEIRO**: Verificar usuário logado com `mcp_github_get_me`
  2. ✅ Criar PR com `mcp_github_create_pull_request`
  3. ✅ **IMEDIATAMENTE** após criar, usar **APENAS** `mcp_github_update_issue` com parâmetro `assignees` (NUNCA usar `mcp_github_update_pull_request` com `reviewers`)
  4. ✅ Substituir OWNER, REPO e ISSUE_NUMBER pelos valores corretos
  5. ✅ **APLICAR REGRA ANTI-AUTO-ATRIBUIÇÃO**: 
     - Se não houver Assignee especificado: usar lógica inteligente baseada no usuário logado
     - Se Assignee especificado = usuário logado: **IGNORAR** e usar lógica inteligente
     - Se Assignee especificado ≠ usuário logado: usar o Assignee especificado
     - **LÓGICA INTELIGENTE** = a regra automática baseada em quem está logado (item 3 acima)
  6. ✅ **NUNCA** incluir o usuário logado no array de assignees

- **🔴 ATENÇÃO CRÍTICA**: NÃO usar `mcp_github_update_pull_request` com `reviewers` - usar APENAS `mcp_github_update_issue` com `assignees`
- NÃO mencionar assignees em comentários, apenas atribuir no campo correto do GitHub.
- SEMPRE atribuir nos assignees, NUNCA nos reviewers.

- **✅ EXEMPLO CORRETO DO COMANDO**:
  ```
  mcp_github_update_issue:
  - owner: "tr"
  - repo: "nome-do-repo"  
  - issue_number: 1234
  - assignees: ["usuário1", "usuário2"]
  ```

- **❌ COMANDO ERRADO (NÃO USAR)**:
  ```
  mcp_github_update_pull_request:
  - reviewers: ["usuário1", "usuário2"]  # ❌ ERRADO!
  ```

### 🔴 LEMBRETE FINAL OBRIGATÓRIO:
**NUNCA TERMINAR UM PR SEM:**
1. ✅ Usar `mcp_github_update_issue` para assignees
2. ✅ Confirmar que o assignee foi atribuído
3. ✅ Verificar se o processo completo foi executado

## Sumarização de PR
1. **APÓS** a criação de qualquer PR, sempre gere automaticamente comentários nas duas plataformas de forma paralela:

### 📱 **FORMATO GITHUB** (Sumarização)
**Título:** "📋 **Sumarização do PR**"
**Estrutura com GitHub Alerts:**
```
> [!IMPORTANT]
> ## 🎯 **O que foi feito?**
> [Resumo breve da mudança]

> [!NOTE]
> ## 🤔 **Por que precisou dessa mudança?**
> [Contexto e problemas identificados]

> [!TIP]
> ## 🔧 **O que mudou de forma resumida?**
> ```diff
> - ANTES: [código/configuração anterior]
> + DEPOIS: [código/configuração nova]
> ```
> [Explicação baseada nas diferenças dos arquivos do PR]

> [!CAUTION]
> ## 📊 **Impacto Resumido**
> [Segurança, Performance, Funcionalidade]

> [!WARNING]
> ## 🚀 **Justificativa Técnica**
> [Versões, compatibilidade, escolhas técnicas]

> [!CAUTION]
> ## ⚠️ **Considerações de Rollback**
> [Como reverter se necessário]

> [!IMPORTANT]
> ## **Resumo e Recomendação**
> [Resumo final para o aprovador do PR ter garantia e poder aprovar]
```

### 💻 **FORMATO ADO** (Documentação Técnica)
**Título:** "📋 **Documentação Técnica**"
**Estrutura HTML com cores INLINE (sem CSS externo) compatível com ADO:**
- 🎯 **Sumário** (background azul)
- 📋 **Análise do Problema** (background roxo)
- 🔧 **Solução Técnica Implementada** (background verde)
- 📊 **Análise de Impacto** (background laranja + tabela)
- 🚀 **Justificativa Técnica** (background rosa)
- ⚠️ **Plano de Rollback** (background vermelho)
- 📋 **Conclusão e Recomendação** (background azul céu)

**IMPORTANTE: Use APENAS estilos INLINE nas divs, sem CSS externo ou classes. Exemplo:**
```html
<div style="background-color: #E3F2FD; border-left: 4px solid #2196F3; padding: 15px; margin: 15px 0; border-radius: 8px;">
<h3 style="color: #333; margin-top: 0;">🎯 Sumário</h3>
<p>Conteúdo aqui...</p>
</div>
```

**CORES EXATAS PARA CADA SEÇÃO:**
- 🎯 **Sumário**: `background-color: #E3F2FD; border-left: 4px solid #2196F3;`
- 📋 **Análise do Problema**: `background-color: #F3E5F5; border-left: 4px solid #9C27B0;`
- 🔧 **Solução Técnica**: `background-color: #E8F5E8; border-left: 4px solid #4CAF50;`
- 📊 **Análise de Impacto**: `background-color: #FFF3E0; border-left: 4px solid #FF9800;`
- 🚀 **Justificativa Técnica**: `background-color: #FCE4EC; border-left: 4px solid #E91E63;`
- ⚠️ **Plano de Rollback**: `background-color: #FFEBEE; border-left: 4px solid #F44336;`
- 📋 **Conclusão e Recomendação**: `background-color: #E0F7FA; border-left: 4px solid #00BCD4;`

**ESTRUTURA COMPLETA OBRIGATÓRIA:**
1. **NÃO usar** `<!DOCTYPE html>`, `<html>`, `<head>`, ou `<style>` tags
2. **Começar diretamente** com as divs estilizadas
3. **Todos os estilos** devem ser INLINE na tag div
4. **Padding obrigatório:** `padding: 15px; margin: 15px 0; border-radius: 8px;`
5. **Headers internos:** usar `<h3 style="color: #333; margin-top: 0;">`
6. **Para tabelas:** usar estilo inline `<table style="width: 100%; border-collapse: collapse;">`

**EXEMPLO COMPLETO DE UMA SEÇÃO:**
```html
<div style="background-color: #E3F2FD; border-left: 4px solid #2196F3; padding: 15px; margin: 15px 0; border-radius: 8px;">
<h3 style="color: #333; margin-top: 0;">🎯 Sumário</h3>
<p><strong>Pull Request #XXXX:</strong> Descrição</p>
<ul>
<li>Item 1</li>
<li>Item 2</li>
</ul>
</div>
```

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
   - Manter análise técnica em ambos os formatos

5. **OBRIGATÓRIO**: Após gerar, poste automaticamente (DE FORMA PARALELA E NÃO SEQUENCIAL) nas duas plataformas usando os MCPs corretos.

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
`Projeto do Azure Devops=onesource-global-trade-next` 

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
