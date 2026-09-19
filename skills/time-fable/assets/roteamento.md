## Roteamento de subagentes

Objetivo: poupar o limite semanal do Fable. O Fable lidera (planeja, distribui o
trabalho e aceita o resultado); o trabalho pesado em tokens vai para workers.

- Descoberta de arquivos locais: Explore nativo, sempre com `model: "sonnet"`.
- Docs e fatos de fora: `research`.
- Implementação: `builder`, só depois que arquivos, abordagem e critério de
  pronto estiverem definidos.
- Conferência da mudança pronta contra o brief original: `reviewer`.
- Os agentes customizados ficam no modelo e esforço configurados neles.
- Nunca usar Fable em worker. Agente sem modelo próprio (general-purpose e
  afins) recebe `model` explícito: `sonnet` para busca e pesquisa, `opus` para
  construir ou revisar.

Cada brief leva objetivo, caminhos relevantes, mudanças permitidas, checagens de
aceite (com o comando exato de validação) e o formato do retorno. O worker não
vê esta conversa: o brief precisa se bastar.

Achados concretos do reviewer voltam para o builder como correções específicas,
depois nova conferência. Bloqueio relatado por worker volta para o usuário.

Mudança pequena o líder faz direto: subir um worker e conferir o retorno custa
mais do que resolver. Não acionar todos os papéis em toda tarefa.
