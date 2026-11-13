# Publicar o PagueProf no GitHub

Repositório da equipe: [Vitorkp/PagueProf](https://github.com/Vitorkp/PagueProf). Esta orientação usa a branch já definida pela equipe: `feature/backend`.

O GitHub deve receber o código e a documentação, nunca o `.env` preenchido, o ZIP privado de deploy ou comprovantes. O arquivo `.gitignore` exclui `.env` e os uploads.

## 1. Atualizar o clone da equipe

Abra o Git Bash dentro da pasta de trabalho da equipe (por exemplo, `E:\PagueProf-git`) e confira o estado antes de copiar arquivos:

```bash
pwd
git status --short --branch
git remote -v
git branch --show-current
```

Confirme que o remoto é `https://github.com/Vitorkp/PagueProf.git` e que a branch atual é `feature/backend`. Se estiver em outra branch, use:

```bash
git switch feature/backend
```

Não crie outra branch: o commit anterior já foi feito em `feature/backend` e o envio falhou por autenticação.

Baixe e extraia `PagueProf-kinghost.zip` fora do repositório, depois copie os arquivos da aplicação para a pasta do clone. **Não copie o arquivo oculto `.env` nem copie a pasta `.git`**. Mantenha `.htaccess`, `.gitignore` e as pastas da aplicação. Não inclua o próprio ZIP.

## 2. Verificar os arquivos antes de preparar o commit

No Git Bash, dentro do clone:

```bash
git status --short
git diff --check
git check-ignore -v .env
git status --short --ignored
```

O `.env` precisa aparecer como ignorado. Confira que a lista não contém `.env`, `PagueProf-kinghost.zip`, arquivos em `storage/comprovantes/` ou credenciais. Se um `.env` já estiver sendo rastreado pelo Git, tire-o apenas do índice e troque as senhas expostas:

```bash
git rm --cached .env
```

## 3. Adicionar, revisar e enviar a branch

```bash
git add -A
git status --short
git diff --cached --check
git diff --cached --name-only
```

Revise a lista staged. Se estiver correta e não mostrar segredos, faça um novo commit com as mudanças copiadas:

```bash
git commit -m "feat: finaliza sistema profissional e cliente"
```

Depois envie a branch existente:

```bash
git push -u origin feature/backend
```

Se o `git commit` responder `nothing to commit`, confira `git status --short --branch`. Pode significar que os arquivos já foram commitados; nesse caso, tente o `git push`.

## 4. Resolver o erro “Invalid username or token”

O GitHub não aceita mais a senha normal da conta em operações HTTPS. A opção mais simples é autenticar pelo navegador usando Git Credential Manager ou GitHub CLI:

```bash
gh auth login
```

Escolha **GitHub.com**, **HTTPS** e a opção de autenticar o Git com as credenciais do GitHub; conclua a autorização no navegador. Depois rode novamente `git push -u origin feature/backend`.

Se não tiver `gh`, tente `git push` e siga a janela de autenticação do Git Credential Manager. Se o Git pedir um token em vez de abrir o navegador, crie um token pessoal no GitHub com acesso de escrita ao repositório `Vitorkp/PagueProf`, cole-o somente no prompt privado do Git, nunca em uma mensagem, arquivo do projeto ou Trello. Não use a senha normal do GitHub e não compartilhe o token.

## 5. Abrir o Pull Request

Depois do envio, abra o repositório no GitHub e crie um Pull Request de `feature/backend` para `main`. Descreva as mudanças, as migrações aplicadas e os testes executados. Peça revisão de outro integrante e só faça merge após o aceite do PO.

Não use `git push --force` para contornar erro de permissão ou substituir trabalho de outra pessoa. Se sua conta não tiver acesso de escrita, peça ao administrador do repositório para conceder permissão de colaborador.
