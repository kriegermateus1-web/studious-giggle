# Diretrizes para agentes: Issues, PRs e Deploys

Este documento descreve o padrão obrigatório para gerenciar tarefas, Pull Requests e deploys neste repositório. Qualquer agente (humano ou automatizado), independente do modelo, deve seguir estas regras ao criar tarefas, propor mudanças e executar deploys.

## Objetivo
Garantir rastreabilidade, revisão e controle seguro de mudanças e deploys. Todas as alterações significativas devem ser rastreadas por uma Issue e entregues via Pull Request que referencia essa Issue.

## Tipos de tarefa (Issues)
- `Correção` — bugs e regressões que precisam ser corrigidos.
- `Melhoria` — melhorias incrementais em funcionalidades existentes.
- `Nova função` — novos recursos ou mudanças de alto impacto.

Cada Issue deve incluir:
- Título claro (ex.: "Correção: botão de contato não abre WhatsApp")
- Descrição com contexto: passos para reproduzir (se aplicável), comportamento atual, comportamento esperado.
- Critérios de aceitação (o que significa "pronto").
- Estimativa (opcional).
- Labels apropriados (`bug`, `enhancement`, `feature`, `priority:high`, etc.).

## Branch naming
- Sempre crie uma branch baseada na branch principal (ex.: `main` ou `master`).
- Nome da branch: `issue-<numero>-descrição-resumida` ou `issue-<numero>/<short-kebab>`.
  - Ex.: `issue-42/add-contact-cta` ou `issue-42/add-contact-cta`

## Pull Request (PR)
Regras para PRs:
1. TODOs e mudanças significativas devem ser referenciadas a uma Issue existente. Na descrição do PR incluir claramente a referência e, quando apropriado, fechar a Issue automaticamente com `Closes #<numero>` ou `Resolves #<numero>`.
2. A descrição do PR deve conter:
   - Referência à Issue: `Related issue: #<numero>` ou `Closes #<numero>`
   - Resumo das mudanças
   - Critérios de aceitação e como testar localmente
   - Checklist de revisão (ex.: testes, lint, build)
3. Não fazer deploys diretamente na branch principal sem PR revisado e checks passing.

Exemplo de descrição de PR:

Related issue: #42

Resumo:
- Corrige o comportamento do botão de contato

Como testar:
1. Acesse a página X
2. Clique no botão Y
3. Verifique que o WhatsApp abre com a mensagem predefinida

Checklist:
- [ ] Código revisado
- [ ] Tests adicionados/atualizados (se aplicável)
- [ ] Build passando

## Deploys
- Deploys devem ser gerenciados por PRs. Merges para a branch principal (ex.: `main`) disparam o pipeline de deploy (CI/CD).
- Todo deploy precisa de uma PR que referencia a Issue correspondente.
- O ambiente de destino deve ser mencionado na descrição do PR (ex.: `deploy: production` ou `deploy: staging`).
- Agentes automatizados não devem executar deploys sem abrir PRs e sem que os checks obrigatórios sejam concluídos com sucesso.

## Regras para agentes (humano ou automático)
- Antes de modificar o repositório: crie uma Issue seguindo os critérios acima.
- Crie uma branch a partir da branch principal usando o padrão de nomeação.
- Faça commits claros e atômicos; inclua referência à Issue no commit quando fizer sentido (ex.: `git commit -m "issue #42: corrige botão de contato"`).
- Abra um PR que referencia a Issue na descrição; inclua os passos de teste e checklist.
- NÃO mescle (merge) sem revisão humana (a menos que exista um fluxo automatizado documentado e acordado) e sem todos os checks obrigatórios em verde.

## Templates e automações recomendadas
- Adicionar templates de Issue e PR em `.github/ISSUE_TEMPLATE/` e `.github/pull_request_template.md` para facilitar a conformidade.
- Labels padronizados: `bug`, `enhancement`, `feature`, `priority:low|medium|high`, `area:frontend|backend|infra`.
- Branch protection rules na branch principal: exigência de PRs, checks passing e revisão aprovada.

## Onde está este documento
Este arquivo (AGENT_GUIDELINES.md) contém o padrão. Qualquer agente deve referenciar este arquivo antes de agir.

---

Se desejar, posso também:
- Criar arquivos de template em `.github/ISSUE_TEMPLATE/` e `.github/pull_request_template.md` automaticamente.
- Criar as Issues iniciais correspondentes a tarefas já conhecidas.
- Atualizar as configurações de branch protection (se você permitir acesso administrativo).
