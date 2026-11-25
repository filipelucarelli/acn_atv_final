# PagueProf

Sistema web responsivo, mobile-first, para profissionais organizarem clientes, serviços, cobranças, recebimentos e comprovantes. Uma conta pode ter perfil profissional, cliente ou os dois.


## Funcionalidades

- Cadastro, login e logout; uma conta pode ter os perfis profissional, cliente ou ambos. Se um profissional cadastrar como cliente o e-mail de uma conta profissional existente, o sistema associa os acessos e avisa quem foi o profissional; o cliente também pode usar “Quero ser um profissional” para ativar o outro perfil. Recuperação de senha por link de uso único, com validade de uma hora.
- Cadastro, edição e desativação lógica de clientes. O campo apresentado na tela é “Observação” (exemplo: “Aluno de Piano”). Cobranças e comprovantes existentes são preservados.
- Geração direta de cobranças sem cadastrar plano: selecionar cliente, serviço, valor, quantidade de 1 a 36 e primeiro vencimento. Tipos de serviço são cadastrados pelo botão “+ Novo serviço” no próprio formulário e selecionados imediatamente. As cobranças seguintes são mensais, mantendo valor e serviço.
- Cada lote usa internamente um registro técnico de série, permitindo sugerir uma nova série após a quitação completa. A sugestão fica em Financeiro > “Sugestão de novas cobranças” e preenche o valor com o da última cobrança paga, que o profissional pode editar para a nova série; as cobranças antigas permanecem intactas. A sugestão pode ser removida sem apagar o histórico. Cobranças pendentes também podem ser editadas individualmente antes de qualquer tentativa de envio de e-mail e antes de receber comprovante.
- Em Recebimentos, o profissional cadastra instruções e, opcionalmente, nome do titular, cidade e chave Pix CPF, celular ou e-mail. O sistema valida e mascara CPF/celular, preserva o e-mail sem máscara, gera localmente um QR Pix estático sem valor e inclui a chave, o QR como imagem inline e o Pix copia-e-cola nos e-mails de cobrança quando configurados. A área do cliente também exibe o QR associado à cobrança.
- O QR Pix é estático: o cliente informa o valor no aplicativo bancário. A aplicação não consulta o banco/DICT, não confirma transferência e não faz conciliação automática. O pagamento continua dependendo da análise manual do comprovante pelo profissional.
- Área do cliente para consultar cobranças, ver as instruções/QR de pagamento e enviar comprovantes. O profissional abre, aprova ou recusa o comprovante; a baixa é manual.
- Financeiro, Relatórios e Calendário mensal exibem o resumo comum de Recebido, Em aberto, Vencido, Rejeitado e Clientes ativos; o calendário mantém também as três categorias coloridas do mês (comprovante enviado, confirmado e sem comprovante). Relatórios inclui filtro por cliente/período, movimentações e gráficos.
- Financeiro em tabela compacta, com abas para cobranças e sugestões. A lista abre por padrão no primeiro e último dia do mês atual; o profissional pode alterar as datas. Os filtros permanecem na tela Financeiro e mostram um estado “Aguarde…” durante a consulta. Cada cobrança com comprovante em análise, paga ou recusada oferece um botão para abrir o arquivo, sem listar anexos à parte na tabela. A ação “Nova” inicia outra cobrança já preenchida com o mesmo cliente, serviço e valor. A lista também permite ordenação e exporta os resultados filtrados para CSV compatível com Excel. A tela Comprovantes abre filtrada em “Em análise”, aceita filtro opcional por período de envio, mostra os registros em tabela responsiva e abre anexos em popup com a marca PagueProf.
- Notificações por e-mail com fila idempotente: somente a cobrança mais próxima é tentada automaticamente após a geração; as futuras aguardam envio manual. O cron diário envia lembretes na véspera e retenta falhas. Avisos ao cliente são suprimidos se a cobrança estiver paga ou cancelada. O envio de comprovante notifica o profissional com links protegidos.

## Tecnologias e arquitetura

- PHP 8.1+, MySQL/MariaDB e PDO, sem framework ou dependências Composer.
- Arquitetura MVC: `index.php` roteia; `app/Controllers`, `app/Models`, `app/Views` e `app/Core` separam responsabilidades.
- HTML, CSS responsivo mobile-first e JavaScript sem framework. QR Codes são gerados localmente no navegador e no servidor, sem enviar chaves Pix a serviços externos. A imagem inline de e-mail usa a extensão PHP GD; se indisponível, o e-mail mantém o Pix copia-e-cola.
- Tabelas e campos do banco em português. O campo de tela “Observação” permanece armazenado na coluna `area_atuacao`.

## Instalação e configuração

A atualização não exige apagar ou recriar uma base existente. Faça backup antes de publicar código novo.

1. Para uma instalação nova, crie uma base MySQL/MariaDB vazia com charset/collation `utf8mb4`/`utf8mb4_unicode_ci`.
2. Selecione essa base no phpMyAdmin e importe somente `database/estrutura.sql`. O arquivo cria as tabelas, mas não executa `CREATE DATABASE` nem `USE`.
3. Para uma base existente, faça backup, selecione a base correta no phpMyAdmin e importe `database/migracoes/20261112_avisos_perfil.sql`, que cria a tabela aditiva para as mensagens de boas-vindas. Se ainda não aplicou a atualização Pix, importe também `database/migracoes/20261030_recebimentos_pix_estatico.sql`. Não reimporte `estrutura.sql` nem apague tabelas. A tabela `planos_cobranca` segue sendo usada apenas para organizar séries internamente.
4. Mantenha `.env` na raiz da aplicação e configure `APP_URL` com domínio canônico e HTTPS. Durante envios pelo site, o sistema identifica a subpasta real pelo caminho de `index.php`; para o cron, mantenha `APP_URL` com a URL completa, sem barra final. Ao usar o repositório, copie `config/.env.example` para `.env` e preencha as credenciais.
5. O ZIP privado de deploy pode conter `.env` configurado. Trate-o como confidencial, não o envie ao GitHub/Trello e não o compartilhe publicamente. Se o envio pelo site ocorrer em `/pagueprof`, os links usam essa pasta mesmo que houvesse um caminho antigo no `.env`; para envios por cron, corrija também o valor de `APP_URL` no servidor.
6. Ative as extensões PHP `pdo_mysql`, `mbstring`, `openssl` e `fileinfo`. Para incluir o QR como imagem no e-mail, habilite também `gd`. A hospedagem precisa permitir conexão SMTP de saída para o envio de e-mails.

Para desenvolvimento local, configure `.env`, inicie `php -S 127.0.0.1:8000` na raiz e acesse `http://127.0.0.1:8000`.

## Fluxo de e-mails e cron

A geração tenta enviar a notificação da cobrança mais próxima. As demais ficam em fila para envio individual pelo profissional. Quando a chave Pix está configurada e a extensão GD está ativa, o aviso inclui a imagem inline do QR estático sem valor, além do titular, chave formatada e código copia-e-cola. Sem GD, o código copiável continua no e-mail. Isso não representa uma confirmação do pagamento. Falhas podem ser retentadas até cinco vezes; o cron agenda lembretes para cobranças que vencem no dia seguinte. Avisos ao cliente são suprimidos após pagamento ou cancelamento. O e-mail sobre comprovante é separado e notifica o profissional. Links gerados durante requisições web usam a subpasta atual da aplicação; o cron usa o `APP_URL` configurado no `.env`.

Configure no `.env` `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `SMTP_SECURITY`, `MAIL_FROM` e `MAIL_FROM_NAME`. Para testar conexão e autenticação sem enviar mensagem, execute por SSH/terminal, dentro da pasta do projeto:

```bash
php scripts/testar-smtp.php
```

O script é restrito à CLI; abrir `scripts/testar-smtp.php` por URL retorna 404 de propósito. Para testar a entrega real, use uma conta cliente de teste com e-mail controlado pela equipe e gere uma cobrança. Programe `scripts/processar-emails.php` no Cron Jobs da KingHost para execução diária. Consulte [deploy KingHost, SMTP e cron](docs/deploy-kinghost.md).

## Rotas principais

As páginas usam `index.php?rota=...`: `login`, `cadastro`, `recuperar_senha`, `nova_senha`, `clientes`, `nova_cobranca`, `cobrancas` (Financeiro, com abas para cobranças e sugestões), `exportar_cobrancas`, `editar_cobranca`, `remover_sugestao`, `minhas_cobrancas`, `comprovantes`, `relatorios`, `calendario` e `configuracoes`. Os serviços são adicionados na tela Cobranças, sem aba separada. As ações verificam sessão, perfil e proteção CSRF no servidor.

## Documentação

- [Planejamento das seis sprints](docs/planejamento-sprints.md)
- [Organização sugerida do Trello](docs/trello.md)
- [Deploy KingHost, SMTP e cron](docs/deploy-kinghost.md)
- [Recebimentos e QR Pix estático](docs/pix-estatico.md)
- [Publicação no GitHub](docs/publicar-github.md)
