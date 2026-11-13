# Trello — organização sugerida para o PagueProf

Quadro de referência informado pela equipe: [Projeto e Desenvolvimento 1](https://trello.com/b/XPZeyPlK/projeto-e-desenvolvimento-1). Use esse quadro se ainda for o espaço oficial; este arquivo é a proposta de organização, não a confirmação de cartões já criados.

## Listas do quadro

1. **Produto e acordos** — objetivo, links, regras, Definition of Done, QR Pix estático sem valor e confirmação manual de comprovantes; Pix dinâmico e integrações ficam no backlog futuro.
2. **Backlog do produto** — histórias ainda não refinadas, ordenadas pelo PO.
3. **Sprint atual — A fazer** — itens aceitos no planejamento, com critérios de aceite.
4. **Em andamento** — trabalho em execução; limitar a um cartão ativo por pessoa ou ao WIP combinado.
5. **Revisão técnica** — PR/alteração aguardando revisão por outra pessoa.
6. **Teste e homologação** — implementação revisada que ainda precisa passar pelos critérios de aceite.
7. **Concluído** — aprovado pelo PO e dentro da Definition of Done.
8. **Impedimentos** — usar apenas para sinalizar bloqueio; manter um responsável e uma próxima ação no cartão.

A Sprint 1 — login — deve permanecer arquivada ou na lista de concluídos como registro da entrega. Na troca de sprint, arquive a lista antiga e mantenha as listas de fluxo; identifique cada cartão com a etiqueta da sprint para preservar o histórico sem criar uma coluna para cada estado de cada sprint.

## Etiquetas

| Etiqueta | Uso |
|---|---|
| `S1` a `S6` | Sprint prevista para a história |
| `Front-end` | Telas e comportamento de interface |
| `Back-end` | PHP, regras, rotas e integração |
| `Banco` | SQL, migração e consulta |
| `Segurança` | Sessão, dados, autorização e arquivos |
| `Produto` | Regra de negócio e aceite |
| `QA` | Testes e evidências |
| `Bloqueado` | Dependência com motivo e plano de remoção |

Use membros do cartão para quem executa. Indique uma pessoa responsável e uma pessoa revisora; a revisão não substitui o aceite do PO.

## Divisão sugerida das próximas sprints

### Sprint 2 — Clientes e serviços

- `[S2][US04] Cadastrar cliente do profissional com campo Observação` — Filipe (regra), Alan (backend), Vitor/Nickolas (interface), Talita (teste).
- `[S2][US05] Vincular conta cliente pelo e-mail e ativar o perfil profissional opcionalmente` — Alan (vínculo transacional, sincronização de perfis e avisos), Filipe (regras e aceite), Vitor/Nickolas (botão, menus e popup), Talita (testar conversões nos dois sentidos e avisos únicos).
- `[S2][US23] Adicionar serviço pelo botão + no formulário de cobrança` — Alan (endpoint/isolamento), Filipe (regra), Vitor/Nickolas (interface sem sair da tela), Talita (teste de serviço novo e duplicado).
- `[S2][US24] Editar ou excluir cliente sem apagar histórico` — Alan (desativação segura), Filipe (regra), Vitor/Nickolas (formulário e ações), Talita (teste de isolamento e histórico).

### Sprint 3 — Cobranças diretas, Financeiro e Relatórios

- `[S3][US07] Gerar cobranças para um ou mais clientes: busca, checkboxes, serviço existente/novo, valor, 1–36 vencimentos mensais, primeiro vencimento e criação atômica em lote` — Alan (validação e série técnica), Filipe (regras), Vitor/Nickolas (formulário e prévia), Talita (datas, limite e bloqueio por instruções).
- `[S3][US08] Financeiro responsivo: mês atual por padrão, filtros com Aguarde, comprovante por linha, Nova baseada na cobrança, cinco totalizadores e exportação Excel` — Vitor/Nickolas (tabela e ações mobile), Alan (consulta protegida, rota dos filtros, somas e exportação CSV), Filipe (campos e opções), Talita (testar período, permanência no Financeiro, comprovantes, totais, pré-preenchimento, ordenação e abertura no Excel).
- `[S3][US27] Editar cobrança pendente antes do primeiro envio` — Alan (bloqueios transacionais e integridade), Filipe (regra e limites), Vitor/Nickolas (formulário e cards móveis), Talita (testar cobrança enviada, tentada e com comprovante).
- `[S3][US25] Cancelar cobrança não paga sem apagar histórico` — Alan (transição e notificações), Filipe (regra), Vitor/Nickolas (ação e confirmação), Talita (teste de comprovantes e cobrança paga).
- `[S3][US09] Consolidar indicadores da antiga Visão geral em Relatórios e manter calendário mensal separado` — Alan (consultas e somas), Filipe (aceite), Vitor/Nickolas (gráficos, filtros e calendário), Talita (reconciliação das categorias). Relatórios e calendário compartilham os totalizadores Recebido, Em aberto, Vencido, Rejeitado e Clientes no período; o calendário preserva os totais por categoria.
- `[S3][US26] Exibir sugestão de renovação com valor herdado e editável` — Alan (buscar último valor pago, validar e persistir o valor novo sem alterar cobranças antigas), Filipe (regra de negócio), Vitor/Nickolas (campo editável responsivo e remoção), Talita (testar valor padrão, edição, quitação integral e filtro por cliente).

### Sprint 4 — Área do cliente, Pix estático e comprovantes

- `[S4][US10] Exibir pagamentos do cliente` — Vitor/Nickolas (portal e filtros por status, profissional e vencimento; padrão “A pagar”), Alan (autorização/consulta), Filipe (regras).
- `[S4][US11] Enviar comprovante protegido` — Alan (upload e autorização), Vitor/Nickolas (formulário), Talita (formatos/limites).
- `[S4][US12] Aprovar ou recusar comprovante` — Filipe/Alan (status e histórico), Vitor/Nickolas (tela), Talita (teste de autorização).
- `[S4][US28] Filtrar e revisar comprovantes em tabela responsiva` — Alan (filtros/status e autorização), Vitor/Nickolas (tabela, popup com marca PagueProf e layout mobile), Filipe (fluxo de revisão), Talita (teste de status, datas e PDF/imagem).
- `[S4][US27] Configurar chave Pix CPF/celular/e-mail e exibir QR estático sem valor` — Alan (BR Code, CRC e validação), Filipe (recebedor/instruções), Vitor/Nickolas (máscara, QR e cópia), Talita (testar CPF, telefone +55, e-mail, QR e aviso de confirmação manual).

### Sprint 5 — Notificações por e-mail

- `[S5][US14] Avisar cliente sobre a cobrança mais próxima com titular/chave e Pix copia-e-cola se configurados; manter futuras na fila manual` — Alan (fila idempotente/SMTP), Filipe (regra e texto), Vitor/Nickolas (conteúdo e link), Talita (teste de repetição e estados).
- `[S5][US15] Enviar lembrete um dia antes do vencimento` — Alan (cron/consulta), Filipe (regra de vencimento), Talita (timezone e horário), Vitor/Nickolas (clareza da mensagem).
- `[S5][US16] Suprimir avisos ao cliente quando cobrança for paga ou cancelada` — Alan/Filipe (transição e fila), Talita (pagamento antes do cron e retentativa), Vitor/Nickolas (mensagem de estado).
- `[S5][US21] Avisar profissional sobre comprovante com links protegidos` — Alan (fila e envio SMTP), Filipe (dados/regras), Talita (reenvio e autorização), Vitor/Nickolas (links e tela de análise).

### Sprint 6 — Qualidade e publicação

- `[S6][US17] Rodar regressão de profissional e cliente` — Talita coordena; cada integrante testa fora do próprio módulo.
- `[S6][US18] Publicar versão KingHost` — Alan (deploy), Filipe (backup/aceite), Talita (checklist), Vitor/Nickolas (smoke test visual).
- `[S6][US19] Confirmar segurança e documentação da release` — Talita (evidências), Alan (permissões/logs), Filipe (aceite), frontend (mobile/acessibilidade).
- `[S6][US22] Homologar recuperação de acesso com token de uso único` — Alan (token e persistência), Filipe (regras de segurança), Vitor/Nickolas (telas), Talita (expiração e uso único).

As séries ficam ligadas a um registro técnico interno usado para referência e sugestões; não há cartão de cadastro manual de plano nem tela de planos.

No início de cada sprint, o PO seleciona o que cabe na capacidade do grupo. Não é obrigatório assumir todas as histórias sugeridas de uma vez.

## Modelo de cartão

**Título:** `[Sx][USxx] Verbo + resultado`

**Descrição:** Como [perfil], quero [objetivo], para [benefício].

**Critérios de aceite:** passos verificáveis, comportamento esperado e erros/estados vazios. Inclua quem pode acessar o dado e qual registro deve ser alterado.

**Checklist:**
- [ ] Regra e critérios alinhados com o PO
- [ ] Implementação concluída
- [ ] Código revisado por outro integrante
- [ ] Testado em mobile e desktop
- [ ] Segurança/autorizações verificadas
- [ ] Evidência anexada sem dados reais
- [ ] Documentação atualizada
- [ ] Aceite do PO

No cartão, registre responsável, revisor, dependências, estimativa, prazo e link de PR/commit. Não coloque senha de banco, credenciais de serviços, tokens, chaves privadas, comprovantes ou dados reais de cliente no Trello.

## Backlog após as seis sprints — versões futuras

- Avaliar Pix dinâmico com valor e vencimento e integração para confirmação automática por cobrança.
- Avaliar um provedor de pagamentos; Mercado Pago é uma possibilidade, ainda sem decisão ou implementação.
- Planejar conciliação automática por API/webhook, OAuth por profissional, estornos e homologação somente quando a equipe priorizar essa evolução.
