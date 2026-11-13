# PagueProf — planejamento de seis sprints

## Objetivo e estado do projeto

O plano contém **seis sprints no total**. A **Sprint 1 — Login e acesso — está entregue** e deve permanecer registrada como tal. Relatórios antigos de descoberta/protótipo pertencem ao histórico do repositório, não ao pacote de instalação. A implementação atual inclui cadastro/edição/desativação de clientes; vínculo de contas existentes por e-mail com ativação dos perfis Profissional/Cliente, popup único com o nome do profissional e ativação autônoma de perfil profissional pelo cliente; tipos de serviço adicionados dentro do formulário de cobrança; geração direta de cobranças em séries de 1 a 36 vencimentos mensais; portal do cliente; comprovantes; confirmação manual; Financeiro ordenável; Relatórios com indicadores/gráficos; calendário mensal com filtro e totalizadores; recuperação de senha; e notificações por e-mail para cliente e profissional. A aplicação mantém um registro técnico interno da série para oferecer sugestões após a quitação, mas não exige que o profissional cadastre um plano. Esses incrementos ainda precisam de revisão e aceite da equipe. Pix estático sem valor, por chave CPF ou celular, está implementado em Recebimentos. Pix dinâmico, integração com provedor, Mercado Pago e conciliação automática ficam fora do escopo e estão listados no fim como possíveis versões futuras. A publicação final na KingHost ainda deve ser feita e validada pela equipe.

A duração sugerida é de duas semanas por sprint, a confirmar pelo time. As sprints abaixo são um plano de trabalho e aceite, não um registro de que todas já foram aprovadas pelo PO.

## Equipe

| Integrante | Papel principal | Responsabilidade no projeto |
|---|---|---|
| Filipe | PO e Back End | Priorizar e aceitar histórias, definir regras financeiras e apoiar autenticação/integrações |
| Vitor | Front End | Construir e revisar telas responsivas e componentes da aplicação |
| Talita | Scrum Master | Facilitar cerimônias, acompanhar fluxo, impedimentos, testes e documentação |
| Alan | Back End | Implementar MVC, banco, regras, relatórios e integrações |
| Nickolas | Front End | Formulários, estados de tela, acessibilidade e comportamento mobile |

O PO valida o resultado; a Scrum Master facilita o processo e não assume sozinha a responsabilidade pela entrega.

---

## Sprint 1 — Login e controle de acesso (ENTREGUE)

**Meta:** Entregar a área de autenticação para profissional, cliente ou usuário com os dois acessos.

**Histórias:**
- **US01 — Entrar:** Como usuário, quero entrar com e-mail e senha para acessar minhas áreas.
- **US02 — Criar conta:** Como usuário, quero escolher acesso de profissional, cliente ou ambos.
- **US03 — Encerrar sessão:** Como usuário autenticado, quero sair e encerrar minha sessão.

**Tarefas:** Tela responsiva de login e cadastro; roteamento PHP/MVC; tabelas `usuarios`, `perfis` e `usuarios_perfis`; senhas com hash; sessão segura; validação CSRF; consultas PDO preparadas; proteção de rotas.

**Responsáveis sugeridos:** Filipe e Alan — backend, autenticação e banco; Vitor e Nickolas — tela e mobile; Talita — critérios e revisão do fluxo.

**Critérios de aceite:** Login válido abre área protegida; senha inválida não autentica; o usuário pode escolher um ou os dois perfis; senha não é salva em texto puro; rota privada exige sessão; logout encerra a sessão; tela funciona em celular e desktop.

**Registro:** Sprint concluída conforme a entrega já existente. O protótipo inicial permanece apenas no histórico do repositório e não é incluído no pacote de produção.

---

## Sprint 2 — Clientes e serviços

**Meta:** Permitir ao profissional manter sua carteira de clientes e cadastrar serviços dentro do fluxo de cobrança, sem uma aba separada.

**Histórias:**
- **US04 — Cadastrar cliente:** Como profissional, quero registrar nome, e-mail, telefone e observação para organizar minha carteira.
- **US05 — Vincular e ampliar acesso:** Como usuário, quero que um profissional me associe pelo e-mail ao cadastro de cliente, mantendo os dois perfis; como cliente, quero ativar também o perfil profissional quando desejar.
- **US23 — Adicionar serviço na cobrança:** Como profissional, quero criar uma nova alternativa de serviço pelo botão “+” no formulário de cobrança e selecioná-la sem sair da tela.
- **US24 — Editar ou excluir cliente:** Como profissional, quero corrigir os dados ou desativar um cliente sem apagar cobranças e comprovantes anteriores.

**Tarefas:** Tabela `clientes` ligada ao profissional e, quando existente, à conta do cliente; ao associar uma conta profissional existente, conceder perfil cliente de modo transacional e registrar aviso único com o nome do profissional; sincronizar os perfis da sessão; botão POST protegido “Quero ser um profissional” para contas apenas cliente, criação da configuração profissional e aviso de boas-vindas; migração aditiva da tabela `avisos_perfil`; cadastro/edição com campo “Observação”; desativação lógica; isolamento por profissional; cadastro de serviço dentro do formulário de cobrança; indicador de cliente em dia ou com atraso.

**Responsáveis sugeridos:** Filipe — regras e aceite; Alan — modelo, banco e validação; Vitor — telas; Nickolas — responsividade e estados vazios/erro; Talita — teste de isolamento e critérios.

**Critérios de aceite:** O profissional só consulta, edita e desativa clientes da própria conta; e-mail associa a conta sem duplicar cadastro; se um novo vínculo de cliente for criado para usuário com perfil profissional, a associação garante o perfil cliente no banco e mostra uma vez um popup com o nome do profissional; se o perfil cliente foi concedido agora, o texto informa “Seu perfil mudou para Profissional e Cliente”; se a conta já era dual, o texto confirma que ambos os perfis seguem ativos; uma conta apenas cliente mostra “Quero ser um profissional”, cuja confirmação CSRF cria o perfil profissional, abre os menus profissional e cliente e exibe boas-vindas; perfis já existentes não são duplicados nem recebem repetidamente o aviso; desativar cliente impede novas cobranças e preserva histórico; serviços são isolados por profissional; um novo serviço pode ser criado e selecionado sem abrir outra página.

## Sprint 3 — Cobranças diretas, Financeiro e Relatórios

**Meta:** Gerar cobranças sem cadastro manual de plano e consultar os recebimentos, pendências e séries concluídas.

**Histórias:**
- **US07 — Gerar série de cobranças:** Como profissional, quero selecionar um ou mais clientes por busca e checkboxes e informar serviço existente ou recém-adicionado, valor, quantidade de 1 a 36 e primeiro vencimento para criar séries mensais independentes com os mesmos dados, sem cadastrar um plano previamente.
- **US08 — Consultar, organizar e exportar o Financeiro:** Como profissional, quero filtrar por cliente, período e situação, ordenar a lista, abrir o comprovante pela cobrança e exportar o resultado para abrir no Excel.
- **US27 — Ajustar cobrança antes do envio:** Como profissional, quero editar serviço, valor ou vencimento de uma cobrança pendente antes de qualquer tentativa de e-mail e antes de receber comprovante, sem alterar as demais cobranças da série.
- **US25 — Cancelar cobrança:** Como profissional, quero cancelar uma cobrança incorreta sem apagar comprovantes nem o histórico financeiro.
- **US09 — Consultar relatórios:** Como profissional, quero ver indicadores, gráficos, movimentações e o calendário mensal em páginas de relatório apropriadas.

**Tarefas:** Cobranças com referência única; validar instruções de pagamento no servidor e na interface; gerar mensalmente as datas seguintes preservando o dia de referência quando possível; gravar o lote como série técnica interna, sem formulário de cadastro de plano; tentar só a notificação inicial automaticamente e deixar as próximas para envio manual; criar Nova cobrança com lista pesquisável de clientes, checkboxes e seleção clara de um ou mais destinatários; aplicar os mesmos dados às séries dos selecionados e gravar todas em uma única transação; criar Financeiro com abas de cobranças e sugestões, filtro inicial do primeiro ao último dia do mês atual (editável), ordenação por vencimento, cliente, valor ou situação e feedback “Aguarde…” ao aplicar filtros; padronizar os cartões de valores em Financeiro, Relatórios e Calendário; exibir “Clientes no período” em uma linha compacta fora dos cartões, nas três telas, como clientes distintos com cobrança no filtro / total de clientes ativos (ex.: 8/10); no Calendário, manter essa linha junto ao cabeçalho do mês e apresentar Total geral por vencimento e categorias dentro do calendário; mostrar um botão para abrir o comprovante junto à cobrança em análise, paga ou recusada, sem exibir uma lista de anexos abaixo da tabela; oferecer ação “Nova” em cada cobrança, pré-preenchendo cliente, serviço e valor; permitir edição individual apenas antes de tentativa de e-mail e comprovante; permitir remover uma sugestão sem apagar a série paga; preencher a renovação com o valor da última cobrança paga, permitir ajuste e aplicar o valor informado somente às novas cobranças; incorporar ao Relatórios os indicadores da antiga Visão geral, gráficos e movimentações; sugerir uma nova série somente após todas as cobranças da série atual serem pagas; manter calendário separado, filtrável por mês/cliente, com valores por vencimento, total geral e totalizadores separados para confirmado, em análise, sem comprovante, vencido e recusado, além das cinco cores discriminadas na legenda.

**Responsáveis sugeridos:** Alan — consultas, integridade e geração; Filipe — regras monetárias e aceite; Vitor e Nickolas — Financeiro, filtros e relatório; Talita — conferir cenários de datas e valores.

**Critérios de aceite:** A geração aceita um ou mais clientes ativos do profissional selecionados por busca/checkbox, serviço cadastrado e valor positivo; todas as séries dos clientes marcados usam os mesmos parâmetros e são gravadas atomicamente (se um cliente ou parâmetro for inválido, nenhuma série do lote é criada); a quantidade aceita somente 1 a 36; cada novo vencimento usa o mesmo valor/serviço e datas mensais; instruções de pagamento são obrigatórias; cobranças novas ficam vinculadas internamente à série para idempotência e sugestão, sem tela ou etapa de cadastro de plano; uma notificação inicial é tentada e as demais aguardam envio manual; ao abrir o Financeiro, o período inicial cobre o mês atual do dia 1 ao último dia, pode ser alterado e, ao aplicar os filtros, a tela continua no Financeiro com feedback “Aguarde…”; o Financeiro permite organização sem interpolar parâmetros livres na consulta e exporta para CSV compatível com Excel usando exatamente os filtros e a ordenação selecionados; cobranças em análise, pagas ou recusadas que tenham arquivo mostram um botão “Comprovante” na própria linha, sem lista separada abaixo da tabela; a página Comprovantes inicia em “Em análise”, filtra por status e, mediante checkbox, por data inicial/final de envio, exibe tabela compacta responsiva e abre PNG/JPG/PDF em popup com a marca do sistema; cada cobrança oferece “Nova”, que abre a geração com cliente, serviço e valor pré-preenchidos; cobranças não notificadas e sem comprovante podem ser ajustadas individualmente; Relatórios traz os quatro indicadores do resumo geral; a sugestão fica na aba Financeiro, só aparece depois de quitada toda a série, preenche o valor com o da última cobrança paga, permite editá-lo para a nova série sem alterar cobranças anteriores, nunca gera sem ação do profissional e pode ser removida sem eliminar histórico; “TODOS” preserva a visão consolidada; Financeiro e Relatórios mostram Total geral (Recebido + Em aberto, sem dupla contagem), Recebido, Em aberto, Vencido e Rejeitado em cartões e “Clientes no período” em uma linha compacta, no formato clientes distintos com cobrança / total de clientes ativos; essa cobertura segue os filtros de período, cliente e, no Financeiro, situação; o Calendário usa a mesma grade e visual dos cartões de valores de Relatórios, mostra “Clientes no período” em uma linha compacta junto ao cabeçalho do mês e mantém Total geral mensal e cinco categorias em cartões iguais, sem repetição; rejeitados são roxos nas telas financeiras e roxos riscados no calendário, separados dos vencidos laranja; as datas de apuração são claras e o filtro de situação não altera os totais; o calendário diferencia pago (verde), em análise (azul), em aberto sem comprovante (preto), vencido (laranja) e recusado (roxo riscado), apresenta total geral sem dupla contagem e totalizadores de cada categoria; cobrança paga não pode ser cancelada e o histórico é preservado; em telas até 899px, a navegação profissional é recolhida num menu hambúrguer acessível; nomes longos no cabeçalho são truncados sem ultrapassar a tela; os cartões de clientes mantêm nome, e-mail, situação e ações legíveis sem comprimir os textos.

## Sprint 4 — Área do cliente, Pix estático e comprovantes

**Meta:** Exibir cobranças e opção Pix estática ao cliente e permitir envio, análise e baixa manual de comprovantes.

**Histórias:**
- **US10 — Ver meus pagamentos:** Como cliente, quero visualizar minhas cobranças pagas, a pagar e vencidas, filtrando por status (abrindo em “A pagar”), profissional (incluindo “Todos”) e, quando ativar o checkbox, pelo vencimento inicial e final.
- **US11 — Enviar comprovante:** Como cliente, quero anexar um comprovante para o profissional conferir.
- **US12 — Conferir comprovante:** Como profissional, quero aprovar ou recusar um comprovante e registrar uma observação.
- **US27 — Mostrar Pix estático:** Como cliente, quero ver o QR Pix sem valor e o código copia-e-cola por cobrança, com o titular e a chave identificados.

**Tarefas:** Tela de pagamentos vinculada à conta pelo e-mail, abrindo por padrão no status “A pagar” e oferecendo filtro de status, filtro de profissional (opção “Todos”) e checkbox que habilita vencimento inicial/final; gerar e mostrar o BR Code Pix estático sem valor por cobrança quando o profissional cadastrar chave CPF/celular/e-mail, nome e cidade; validar e-mail de chave Pix até 77 caracteres sem aplicar máscara; permitir copiar o código; upload limitado a PDF/JPG/PNG e 5 MB; armazenamento fora de acesso HTTP direto; página profissional de comprovantes com filtro inicial em análise, filtros por status e por período opcional, tabela responsiva e popup com marca PagueProf para PNG/JPG/PDF; aprovação/recusa com registro; atualização segura de status e histórico.

**Responsáveis sugeridos:** Vitor e Nickolas — portal e estados responsivos; Alan — autorização, upload e consulta; Filipe — estados financeiros e regras; Talita — teste ponta a ponta, acessibilidade e critérios.

**Critérios de aceite:** Cliente vê apenas suas cobranças; o QR estático não contém valor e a chave telefone usa formato internacional +55; o cliente informa o valor no banco e a baixa só ocorre após aprovação manual; comprovante só pode ser anexado a cobrança própria ainda não paga; arquivos fora da lista permitida são rejeitados; somente o profissional dono pode abrir/analisar; Comprovantes abre em análise, permite selecionar todos/aprovados/recusados e aplicar datas de envio, mostra tabela compacta no desktop e cartões claros no celular, e abre o arquivo num popup com a marca PagueProf; ações Confirmar/Recusar só aparecem em comprovantes em análise; aprovação dá baixa e recusa mantém histórico; série só pode ser sugerida depois que todas as cobranças estiverem pagas.

## Sprint 5 — E-mails, fila idempotente e lembretes

**Meta:** Enviar ao cliente somente avisos de cobranças elegíveis e lembrete um dia antes do vencimento; avisar separadamente o profissional quando um comprovante for recebido.

**Histórias:**
- **US14 — Avisar nova cobrança:** Como cliente, quero receber por e-mail valor, vencimento, link de pagamentos e, quando configurados, titular/chave Pix e código copia-e-cola sem valor.
- **US15 — Receber lembrete:** Como cliente, quero receber um lembrete um dia antes do vencimento se a cobrança ainda estiver pendente.
- **US16 — Evitar avisos indevidos:** Como cliente, não quero receber aviso de cobrança paga ou cancelada.
- **US21 — Avisar o profissional sobre comprovante:** Como profissional, quero receber os dados da cobrança e links protegidos para abrir o comprovante e a tela de análise.

**Tarefas:** Fila persistida e idempotente; tentar automaticamente somente o aviso da cobrança mais próxima; manter cobranças futuras aguardando envio manual pelo profissional; entrega por SMTP com os dados Pix copiados da configuração validada (sem valor no BR Code); cron CLI diário para agendar lembretes um dia antes do vencimento e retentar falhas; checagem do estado antes de enviar; suprimir avisos ao cliente após pagamento/cancelamento; limitar novas tentativas a cinco; avisar o profissional quando recebe comprovante, com links que exigem autenticação para abrir e analisar; logs sem credenciais; documentação KingHost. O e-mail de comprovante é independente e continua informando uma ação já feita pelo cliente mesmo se a cobrança for baixada.

**Responsáveis sugeridos:** Alan — fila, serviço SMTP e cron; Filipe — regra de negócio, texto e aceite; Vitor e Nickolas — conteúdo claro, links e leitura em celular; Talita — teste de vencimento, repetição, pagamento prévio e falha de SMTP.

**Critérios de aceite:** Cada cobrança gera no máximo uma notificação inicial; somente a próxima cobrança é tentada automaticamente e as demais aguardam ação manual; sem SMTP, a cobrança persiste e o aviso pode ser recuperado; lembretes são agendados para um dia antes; avisos ao cliente não são enviados para cobrança paga/cancelada; quando configurado, o e-mail usa a chave formatada e o payload correspondente à cobrança, sem prometer confirmação automática; falhas têm no máximo cinco tentativas; a notificação de comprovante vai ao profissional com acesso autenticado, sem aprovar automaticamente; logs não expõem credenciais.

**Dependências:** A equipe precisa obter os dados SMTP de um provedor autorizado e configurar um Cron Job na hospedagem. Sem isso, o recurso está implementado, mas não há garantia de entrega nem do lembrete diário.

---

## Sprint 6 — Testes, segurança e publicação KingHost

**Meta:** Concluir a recuperação de acesso, homologar os fluxos, corrigir pendências, publicar a versão aprovada e orientar a operação.

**Histórias:**
- **US17 — Validar o sistema:** Como equipe, quero testar os fluxos principais em desktop e mobile antes do lançamento.
- **US18 — Publicar aplicação:** Como equipe, quero enviar o projeto à KingHost com HTTPS, PHP e MySQL configurados.
- **US19 — Operar com segurança:** Como administrador, quero manter segredos, comprovantes, backups e logs protegidos.
- **US22 — Recuperar acesso:** Como usuário, quero receber um link seguro, temporário e de uso único para redefinir minha senha sem depender de suporte manual.

**Tarefas:** Revisão cruzada; testar login/cadastro e isolamento por profissional/cliente; validar filtros e totalizadores do calendário; testar cobrança, cancelamento, upload/aprovação de comprovante, validação/máscara da chave Pix, QR estático e e-mails; aplicar em base existente a migração aditiva de Recebimentos depois de backup; conferir recuperação de senha (token de uso único, expiração, limitação e resposta que não revela cadastro); verificar extensões PHP; importar somente `database/estrutura.sql` em base nova; manter `.env` fora do Git; configurar SSL, permissões, URL pública e cron CLI; executar smoke test; documentar backup, reversão e retrospectiva.

**Responsáveis sugeridos:** Talita — plano de teste, coordenação e retrospectiva; Alan — build/configuração e correções; Filipe — aceite, credenciais e plano de reversão; Vitor e Nickolas — inspeção visual, acessibilidade e responsividade.

**Critérios de aceite:** Suíte de regressão e checklist aprovados; backup validado; nenhum segredo no Git; `/storage` não expõe comprovantes; HTTPS ativo; login, cadastro, recuperação, cobrança e baixa manual conferidos na hospedagem; token de redefinição expira e só pode ser usado uma vez; teste SMTP de autenticação aprovado e mensagem de homologação recebida; cron executado; avisos ao cliente são suprimidos após pagamento/cancelamento, e e-mail de comprovante chega ao profissional com links protegidos; falhas respeitam limite sem duplicidade; PO aprova a release.

---

## Cerimônias e Definition of Done

- **Planejamento:** escolher histórias priorizadas, capacidade, dependências e critérios testáveis.
- **Daily:** até 15 minutos, andamento e impedimentos.
- **Review:** demonstrar o incremento; o PO registra aceite ou pendências.
- **Retrospectiva:** registrar o que manter, melhorar e ações com dono e prazo.
- **Definition of Done:** código revisado por outra pessoa; autorização verificada no servidor; erros e estados vazios testados; responsividade conferida; sem segredo em Git/Trello/log; instruções atualizadas; critérios aceitos pelo PO.

## Próximas versões, fora do escopo deste projeto

- Pix dinâmico por cobrança, com valor e vencimento; integração com API para confirmação automática.
- Integração com um provedor de pagamentos — Mercado Pago pode ser avaliado — para confirmação automática por API/webhook.
- OAuth por conta de profissional, idempotência na criação de ordens, tratamento de estorno e homologação de pagamentos.
