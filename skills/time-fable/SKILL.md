---
name: time-fable
description: Instala e configura o time de subagentes que poupa o limite semanal do Fable 5.1 no Claude Code (Fable lidera; research no Sonnet, builder e reviewer no Opus, esforço médio), mais a regra de roteamento global e a rede de segurança no settings.json. Use quando o usuário pedir para instalar, atualizar, conferir ou remover o time de agentes, ou reclamar que o limite do Fable acaba rápido.
---

# Time do Fable

Monta a configuração em que o Fable só planeja, delega e aceita, e o trabalho
pesado em tokens roda em modelos mais baratos. Os arquivos prontos ficam em
`assets/`, ao lado deste SKILL.md. Responda no idioma do usuário.

`BASE` é a pasta de configuração do Claude Code: `~/.claude` (no Windows,
`C:\Users\<usuario>\.claude`), ou outra pasta se o usuário indicar. Não precisa
instalar nada. Para os arquivos de agente, prefira cópia bruta (`cp` no Bash ou
`Copy-Item` no PowerShell), que preserva acentos e quebras de linha; para o
resto, Read, Write e Edit.

Regra de ouro: nunca sobrescrever o que a pessoa já tem sem mostrar a diferença
e perguntar. Nunca colar no chat o conteúdo do `settings.json` (pode ter token).

## Instalar

### 1. Agentes

Para cada arquivo de `assets/agents/` (`research.md`, `builder.md`, `reviewer.md`),
olhe `BASE/agents/<arquivo>`:

- Não existe: copie o arquivo inteiro, sem alterar nada (o bloco `---` do topo
  precisa ficar intacto).
- Existe e é igual: pule.
- Existe e é diferente: mostre o que muda, pergunte, e se for substituir guarde
  o antigo como `<arquivo>.bak`.

### 2. Regra de roteamento

O texto está em `assets/roteamento.md`. Em `BASE/CLAUDE.md`:

- Arquivo não existe: crie com a linha `# Instruções globais (valem em todos os
  projetos)`, uma linha em branco e o texto.
- Existe sem a seção `## Roteamento de subagentes`: acrescente o texto no fim,
  com exatamente uma linha em branco separando do conteúdo anterior.
- Existe com a seção: troque só essa seção (do título até o próximo `## ` ou o
  fim do arquivo). O resto fica como está.
- Se houver outra regra de delegação que conflite (por exemplo, mandar usar o
  modelo principal em subagente), mostre o trecho e pergunte antes de mexer.

### 3. Rede de segurança no settings.json

Em `BASE/settings.json`, garanta `env.CLAUDE_CODE_SUBAGENT_MODEL` igual a
`"sonnet"`, preservando todo o resto:

- Arquivo não existe: crie com `{ "env": { "CLAUDE_CODE_SUBAGENT_MODEL": "sonnet" } }`.
- Existe sem `env`: acrescente o bloco `env`.
- Existe com `env`: acrescente só a chave.
- A chave já existe com outro valor: não mude; avise e pergunte.
- Se `CLAUDE_CODE_SUBAGENT_MODEL_FORCE` estiver ligado, avise que ele força o
  mesmo modelo em todos os agentes e anula o `opus` do builder e do reviewer.

Depois confirme que o arquivo continua sendo JSON válido. Efeito: subagente sem
modelo definido cai no Sonnet em vez de herdar o Fable. O `model` do próprio
agente e o `model` passado na chamada continuam valendo mais do que essa variável.

### 4. Projeto atual (opcional, pergunte antes)

Ofereça acrescentar ao `CLAUDE.md` do projeto aberto uma seção curta
`## Subagentes` com: o comando exato de validação do projeto (descubra nos
scripts: typecheck, test, lint), a regra de que worker nunca faz commit, push
nem deploy (isso é do líder), e a de que regras inegociáveis do projeto valem
para os workers. Se o projeto tem regra de commit, siga a regra dele.

### 5. Fechar

Entregue um resumo curto do que foi criado, pulado ou alterado, e avise:

1. Os agentes só aparecem em uma sessão nova quando a pasta `agents` acabou de
   ser criada. Teste: abrir sessão nova e pedir "liste os agentes disponíveis";
   devem aparecer `research`, `builder` e `reviewer`.
2. O esforço da sessão pesa mais que tudo isso junto: `high` é o padrão
   recomendado; `max` custa cerca de 2,5 vezes mais por um ganho pequeno e vale
   só para problema realmente difícil. Troca-se no seletor de esforço do app (ou
   `/effort high` no terminal).
3. Delegar também gasta: mudança pequena continua sendo feita direto.

## Conferir

Se o usuário só quer saber se está instalado: verifique os três agentes (nome,
`model`, `effort`), a seção no `BASE/CLAUDE.md` e a chave no `settings.json`, e
responda em uma tabela curta.

## Remover

Apague os três arquivos de `BASE/agents/` (só se forem os desta skill), a seção
`## Roteamento de subagentes` do `BASE/CLAUDE.md` e a chave
`CLAUDE_CODE_SUBAGENT_MODEL` do `settings.json`. Confirme com o usuário antes.

## Notas

- `PowerShell` na lista de ferramentas só existe no Windows; em Mac e Linux é
  ignorado e o `Bash` cobre.
- builder e reviewer têm lista de ferramentas enxuta (sem conectores MCP) para
  reduzir o contexto fixo de cada worker. Tarefa que precisa de conector fica
  com o líder.
- A ideia do time (líder caro, workers baratos) vem do vídeo e do guia "Give
  Fable a team", de Brad Bonanno. Os prompts desta skill são texto próprio.
  Referência oficial: https://code.claude.com/docs/en/sub-agents
