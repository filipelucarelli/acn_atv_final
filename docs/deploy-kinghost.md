# Publicação do PagueProf na KingHost

## Antes de enviar

O ZIP privado de deploy fornecido à equipe contém na raiz um `.env` preenchido com as credenciais de banco e SMTP enviadas pela equipe. **Trate o ZIP e o `.env` como confidenciais**: não os envie ao GitHub/Trello nem os compartilhe publicamente. Se estiver instalando a partir do repositório, use `config/.env.example` e preencha o `.env` no servidor. Use o usuário MySQL autorizado no painel; não conecte como `root`.

Confirme no painel KingHost qual PHP está ativo. Requer PHP 8.1+ e extensões `pdo_mysql`, `mbstring`, `openssl` e `fileinfo`. Habilite também `gd` para incorporar a imagem QR aos e-mails; sem GD, o aviso mantém o código Pix copia-e-cola em texto. Para envio SMTP, a hospedagem deve permitir conexão de saída ao provedor de e-mail. Use HTTPS para proteger login, sessões e comprovantes.

## 1. Conferir a base MySQL

A atualização não exige limpar o banco. Faça backup antes de publicar. Para uma base existente, importe `database/migracoes/20261112_avisos_perfil.sql` pelo phpMyAdmin; ela cria somente a tabela aditiva dos avisos de perfil e é repetível. Se ainda não aplicou a migração Pix anterior, importe também `database/migracoes/20261030_recebimentos_pix_estatico.sql`. Para uma instalação realmente nova, crie base MySQL/MariaDB vazia com charset `utf8mb4` e collation `utf8mb4_unicode_ci` e importe somente `database/estrutura.sql`, que já inclui as duas alterações. Não reimporte o schema completo sobre uma base com dados.

O schema usa nomes físicos alinhados ao produto, incluindo `clientes`, `profissional_id`, `cliente_id`, `cliente_usuario_id`, `configuracoes_profissional`, `avisos_perfil`, `observacao_profissional` e `area_atuacao`. Para esta versão em base existente, execute `20261112_avisos_perfil.sql`; não rode scripts legados de outro release nem reimporte o schema completo.

As melhorias do Financeiro e da página de comprovantes não alteram o schema. A ativação/aviso de perfis usa a nova tabela `avisos_perfil`; em base existente, aplique a migração indicada antes de publicar o código.

## 2. Preparar ambiente e domínio

1. No painel, aponte o domínio para a pasta do projeto e habilite SSL/HTTPS.
2. O `.env` do ZIP privado contém os dados de banco e SMTP fornecidos para esta instalação. Confirme no painel que correspondem às contas ativas antes do primeiro acesso; se houver mudança de conta ou senha, atualize o `.env` apenas no servidor. Não copie credenciais para PHP, JavaScript, Trello ou repositório.
3. Configure `APP_URL` com o domínio canônico e HTTPS. Durante uma ação feita pelo site, o sistema identifica a subpasta real a partir do caminho de `index.php`, evitando reutilizar uma pasta antiga como `/pagueprof2`. O cron CLI não tem uma requisição web para inspecionar; nele, `APP_URL` precisa conter a URL completa atual, por exemplo `https://www.seu-dominio/pagueprof`, sem barra final.
4. Confirme que o arquivo oculto `.env` foi extraído junto com `index.php`. No FileZilla, habilite a exibição de arquivos ocultos. Confira SMTP e mantenha `APP_URL` correto para o cron. **Não inclua o `.env` em um commit.**

## 3. Enviar os arquivos

1. Extraia o ZIP no computador.
2. Abra FileZilla ou outro cliente e conecte por SFTP/FTPS se o plano disponibilizar; confirme a pasta pública indicada pelo painel (`www`, `public_html` ou outra).
3. Envie o **conteúdo** do pacote para a raiz do projeto, preservando as pastas `app/`, `assets/`, `config/`, `database/`, `docs/`, `scripts/` e `storage/`, além de `index.php`, `.htaccess` e `.env`. O protótipo HTML antigo não faz parte da aplicação publicada.
4. Não envie `.git/` nem use o ZIP privado em serviços públicos. O GitHub contém apenas o exemplo `config/.env.example`.
5. Se enviar arquivos para uma subpasta, confirme que o domínio abre `index.php` nela. Os links de e-mail enviados pelo site seguem automaticamente essa pasta; mantenha também `APP_URL` atualizado para que lembretes e retentativas por cron apontem para o endereço correto.

O arquivo `.htaccess` bloqueia acesso direto a `.env`, código interno e `storage/`. A hospedagem precisa respeitar regras Apache `.htaccess`. Se a conta estiver em Nginx sem compatibilidade, não exponha `.env`, `app/` ou `storage/` na raiz pública: mova os arquivos privados para fora da raiz web ou configure regras equivalentes no painel antes de produção.

## 4. Pastas e permissões

A aplicação cria `storage/comprovantes/` para arquivos enviados pelos clientes. A pasta precisa ser gravável pelo PHP, sem permissão ampla desnecessária. O arquivo `storage/.htaccess` nega leitura HTTP direta. O profissional acessa comprovantes através de uma rota que verifica a propriedade da cobrança.

Se o envio de comprovantes falhar, confira permissões e o limite de upload do PHP (`upload_max_filesize` e `post_max_size` de pelo menos 5 MB; recomenda-se 8 MB). Não remova a proteção de `storage/`.

O `.env` privado desta entrega já contém as variáveis SMTP configuradas. Se precisar substituí-las, preencha `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `SMTP_SECURITY`, `MAIL_FROM` e `MAIL_FROM_NAME`. O script `php scripts/testar-smtp.php` é somente CLI: por SSH/terminal, verifica conexão e autenticação, mas não envia mensagem; acessá-lo pela URL retorna 404 intencionalmente. Para confirmar entrega real, faça um envio para uma caixa de teste. Notificações com falha podem ser retentadas até cinco vezes, respeitando a fila e o estado da cobrança.

## 5. Recebimento nesta versão e versões futuras

Nesta versão, o cliente segue as instruções, pode escanear QR Pix estático sem valor ou copiar o código e envia comprovante para confirmação manual. O QR é gerado localmente a partir de CPF, celular ou e-mail, titular e cidade; não usa API Pix, não confirma transferências e não concilia pagamentos automaticamente. Pix dinâmico, provedor de pagamento e callbacks seguem fora do código ativo. Essas capacidades estão documentadas como evolução futura no final do [plano de sprints](planejamento-sprints.md). O fluxo novo mantém a tabela `planos_cobranca` apenas como vínculo técnico das séries; o profissional não precisa cadastrar um plano. Não apague essa tabela nem importe o schema sobre um banco com dados existentes. Para esta mudança, preserve a base atual.

## 6. Smoke test depois do envio

1. Abra o domínio em HTTPS e confirme a tela de login e os acentos.
2. Somente em uma instalação nova, importe `database/estrutura.sql` e crie a primeira conta pela tela de cadastro com perfil profissional (ou os dois perfis). Se já existe base com usuários e tabelas, não reimporte o schema; faça login na conta profissional já cadastrada.
3. Cadastre pelo menos dois clientes. Em Nova cobrança, pesquise um nome, marque vários clientes por checkbox, teste “Marcar exibidos” e “Limpar seleção” e confirme o contador/previsão; em desktop, confira que os campos Quantidade de cobranças e Primeiro vencimento ficam alinhados; use “+ Novo serviço” e confirme que o serviço fica selecionado sem abrir outra página. Gere três cobranças mensais para os clientes marcados com o mesmo valor e serviço; confirme as datas e as séries independentes, inclusive um primeiro vencimento no dia 29, 30 ou 31. Confirme que cada cliente recebe somente o aviso inicial e os próximos ficam aguardando no Financeiro. Não é necessário cadastrar um plano.
4. Tente gerar uma cobrança sem instruções de pagamento: o sistema deve alertar e bloquear. Cadastre instruções em Recebimentos e confirme que a geração passa a funcionar. Configure uma chave de teste válida, titular e cidade; confira máscara de CPF/celular e teste também uma chave do tipo E-mail, que deve ser validada e preservada sem máscara; confira QR estático sem valor e botão de cópia. Em base existente, aplique `database/migracoes/20261112_avisos_perfil.sql` antes de publicar; aplique também `20261030_recebimentos_pix_estatico.sql` somente se ainda não tiver sido executada.
5. Gere uma série para um cliente e depois para mais de um cliente; para cada pessoa, confirme que somente o aviso da primeira cobrança é tentado imediatamente e os demais aguardam envio manual. Se a chave Pix estiver configurada e GD ativa, confira no e-mail o titular, a chave correta, a imagem QR inline e o Pix copia-e-cola sem valor. Sem GD, o código copiável deve continuar disponível.
6. Edite um cliente e exclua outro de teste; a exclusão deve desativá-lo e preservar cobranças e comprovantes anteriores.
7. Abra Relatórios e confira que resumo, gráficos e movimentações substituem a antiga Visão geral; filtre cliente e “TODOS” e confira Total geral, Recebido, Em aberto, Vencido, Rejeitado e “Clientes no período” no formato clientes com cobrança/ativos (ex.: 8/10). Confira a mesma cobertura em Financeiro e Calendário; no Financeiro ela deve acompanhar os filtros de data, cliente e situação. Em Financeiro > “Sugestão de novas cobranças”, confirme que a sugestão aparece só após quitar toda a série e que o campo traz o valor da última cobrança paga. Altere o valor, gere uma série de teste e confirme que as novas cobranças usam o valor escolhido e as cobranças quitadas continuam iguais; teste também a remoção da sugestão, que preserva a série no histórico. Abra Calendário mensal e confirme que “Clientes no período” aparece em uma linha compacta sem cartão, tanto no Calendário quanto em Financeiro e Relatórios; em mobile, confirme que a linha no Calendário não mantém altura, margem ou fundo de cartão, com a proporção de clientes com cobranças versus ativos; confira que o Total geral do mês e os cinco totais de categoria usam o mesmo padrão visual dos cartões de Relatórios, sem dupla contagem; vencidos aparecem em laranja e recusados em roxo e riscados. Em Financeiro e Relatórios, confira Total geral = Recebido + Em aberto. Em Relatórios, confira que Movimentações do período aparece em tabela compacta e continua legível no celular. Em desktop, os cinco cartões financeiros preenchem uma linha; no tablet formam linhas equilibradas e no celular o último cartão ocupa a largura disponível, sem espaço vazio pelo indicador em linha.
8. Abra Financeiro sem parâmetros e confira que o filtro inicia no primeiro e no último dia do mês atual. Altere as datas, cliente, situação e ordenação; “Aplicar filtros” deve mostrar “Aguarde…” e manter você no Financeiro. Nas cobranças com situação em análise, paga ou recusada que tenham anexo, use o botão “Comprovante” na linha para abrir o arquivo em popup com marca PagueProf; a tabela não deve mostrar uma lista separada de comprovantes. Em qualquer linha, use “Nova” e confira que cliente, serviço e valor foram reaproveitados na tela de geração. Clique em “Exportar para Excel” e abra o CSV, confirmando acentos, valores e correspondência com os filtros. Edite uma cobrança pendente ainda sem tentativa de e-mail nem comprovante e confira que a alteração vale somente para ela; depois do envio ou de um comprovante, a edição deve desaparecer. Cancele outra cobrança não paga de teste; o histórico deve ser preservado e avisos pendentes suprimidos. Cobrança paga não pode ser cancelada. Abra também Comprovantes: deve iniciar em “Em análise”; teste “Todos”, “Aprovado” e “Recusado”, marque o checkbox de período de envio e aplique datas. Em Meus pagamentos, confirme que a tela abre em “A pagar”; teste o filtro de status (incluindo “Todos”), o filtro de profissional (incluindo “Todos”) e o checkbox de vencimento com datas inicial/final; confirme que, sem o checkbox, todas as datas continuam visíveis; confira o botão de selecionar arquivo para comprovante; no menu, verifique os ícones e o rótulo “Config recebimentos”. Confira que desktop mostra tabela compacta, celular reorganiza as informações sem corte, PNG/JPG e PDF abrem no popup com o logo e as ações Confirmar/Recusar aparecem somente nas linhas em análise. Em uma largura de celular, abra e feche o menu hambúrguer, confirme que “Sair” está dentro dele e não no cabeçalho, confira que “Nova cobrança” é o rótulo do menu, teste um nome longo no cabeçalho e confirme que os cartões de clientes não estreitam nome/e-mail nem geram rolagem horizontal.
9. Teste a mudança entre perfis em contas de homologação: (a) entre como profissional A e cadastre como cliente o e-mail de um usuário B que tenha perfil profissional; entre/atualize a sessão de B e confira o popup “Você agora também é um cliente do profissional A. Seu perfil mudou para Profissional e Cliente.” com OK, o rótulo duplo e os menus de profissional e cliente; (b) em uma conta apenas cliente, clique “Quero ser um profissional” e confira o popup de boas-vindas, o redirecionamento a Relatórios e os dois conjuntos de menus. Recarregue a página e confirme que os avisos não reaparecem. Por fim, crie uma conta cliente com o mesmo e-mail informado pelo profissional, confira o QR/código Pix, envie comprovante e teste a revisão; a confirmação do pagamento continua manual.
10. Confirme que uma cobrança só muda para paga depois que o profissional aprova o comprovante enviado.
11. Na tela de login, solicite recuperação; confira expiração de uma hora, uso único e atualização de senha.
12. Confirme no navegador que `/.env`, `/app/`, `/database/` e `/storage/` não são acessíveis. Não espere que a página pública exiba o conteúdo de `.env`.
13. Confira logs PHP sem copiar tokens, senha ou dados de clientes para tickets.

## 7. Backup e manutenção

Faça backup do banco antes de toda atualização. Guarde o `.env` em local privado e protegido; o `.gitignore` exclui `.env` e uploads de comprovantes. Atualize o código por FTP/SFTP, valide sintaxe/rotas em ambiente de teste antes de produção e mantenha um plano de restauração de arquivos e banco.

## 8. E-mails de cobrança e lembrete

Ao gerar uma ou várias cobranças, só a notificação da primeira é tentada imediatamente. Todas as cobranças do lote ficam ligadas a uma série interna; depois que todas forem pagas, a aba Financeiro > “Sugestão de novas cobranças” pode oferecer a renovação, que só acontece com confirmação manual do profissional. A sugestão pode ser removida sem apagar a série quitada. Cobranças pendentes ainda não notificadas e sem comprovante podem ser editadas individualmente; a alteração não muda outras cobranças do lote. As futuras ficam no estado de espera e o profissional pode enviá-las uma a uma pela tela Financeiro. Se uma cobrança ficar paga ou cancelada, avisos ao cliente são suprimidos. Lembretes da véspera são agendados pelo cron. Quando configurada uma chave Pix e a extensão GD está ativa, os e-mails de cobrança e lembrete incorporam o QR como imagem inline e também mantêm o Pix copia-e-cola. Quando o cliente envia um comprovante, o profissional recebe e-mail com dados e links protegidos para abrir e analisar; ações de aprovação exigem login e CSRF. Em envios iniciados pelo site, o sistema deriva a pasta atual da aplicação da requisição; o cron usa `APP_URL` do `.env`. A recuperação de senha usa link aleatório de uso único, expira em uma hora, limita pedidos por conta e salva somente o hash do token no banco. A resposta da solicitação não revela se o e-mail existe. SMTP precisa estar configurado para mensagens de cobrança, comprovantes e recuperação.

Se a aplicação enviar de uma rede fora do Brasil, a KingHost informa que pode ser necessário ativar SMTP Internacional (SMTPi) no painel e usar `smtpi.kinghost.net`; a ativação pode levar de 30 minutos a 2 horas. A documentação KingHost lista SMTP convencional na porta 587 sem criptografia e a opção TLS/SSL na porta 465; SMTPi oferece portas 587/465. Confirme no painel qual combinação se aplica ao seu plano. Referências: [ativar SMTP internacional](https://king.host/wiki/artigo/como-ativar-o-smtp-internacional/), [SMTP com TLS/SSL](https://king.host/wiki/artigo/como-configurar-uma-conta-de-email-com-criptografia-tls-ssl/) e [configuração SMTP](https://king.host/wiki/artigo/configurar-email-outlook-365/).

Para testar a partir do próprio servidor KingHost sem enviar uma mensagem, execute por SSH/terminal:

```bash
php /caminho/absoluto/do/projeto/scripts/testar-smtp.php
```

O script confere conexão, segurança e autenticação e não exibe usuário ou senha. Se retornar código SMTP 535, confira no painel se a caixa postal existe, se usuário e senha estão corretos e se o SMTPi precisa ser ativado para a origem da conexão. Se alterar dados, atualize o `.env` e rode o teste novamente. O teste bem-sucedido não substitui uma mensagem de homologação; para confirmar entrega, depois envie uma cobrança a uma caixa de teste.

No painel KingHost, procure a função de **Cron Jobs/Tarefas agendadas** e programe a execução diária (por exemplo, às 09:00 no fuso horário desejado) do arquivo `scripts/processar-emails.php` usando o caminho completo do PHP CLI e o caminho absoluto do script. Modelo — substitua pelos caminhos exibidos no painel:

```bash
/caminho/do/php /caminho/absoluto/do/projeto/scripts/processar-emails.php
```

Se o painel oferecer só execução por URL, não exponha esse comando como endpoint HTTP público. Peça à KingHost o caminho correto para PHP CLI/cron ou use um serviço de agendamento externo que invoque de modo autenticado e restrito. Depois rode manualmente o comando uma vez, verifique apenas o total de enviados/falhas no resultado e confira a entrega em uma caixa de teste, incluindo a pasta de spam. Não inclua senha SMTP em argumentos de cron, tickets ou Git.

O sistema pode tentar o aviso de cobrança imediatamente após a geração; o cron é a retentativa para falhas e o mecanismo dos lembretes. Se o cron não rodar, não há garantia de envio na véspera.

## Referências

- [Central de Ajuda KingHost](https://wiki.kinghost.net/)
- [Dados necessários para migração](https://king.host/wiki/artigo/dados-necessarios-para-sua-migracao/)
- [Instalar/usar Composer na KingHost](https://king.host/wiki/artigo/como-instalar-o-composer/) — este projeto não exige Composer.
