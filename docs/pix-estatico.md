# Recebimentos por Pix estático

O PagueProf gera localmente um BR Code Pix estático com uma chave CPF ou celular do profissional. O QR não contém valor; o pagador informa o valor da cobrança no aplicativo do banco. Para ajudar a identificar cada pagamento, as cobranças da área do cliente e os e-mails usam um `txid` próprio da cobrança. O QR genérico da tela Recebimentos usa `***` como identificador.

O BR Code informa a chave Pix, o nome do recebedor, a cidade e os campos de moeda/país. O manual do Banco Central descreve a chave como obrigatória no QR estático, o valor como opcional e o nome do recebedor no exemplo sem valor. Também indica o formato internacional `+55` para chave de telefone celular. A montagem do payload e o CRC16 são feitos no servidor. QRCode.js transforma o texto em imagem no navegador; uma biblioteca PHP local gera o PNG incorporado ao e-mail por MIME/CID. Nenhum dos dois fluxos envia a chave a um gerador externo.

Os e-mails de cobrança e lembrete incluem a imagem inline quando a extensão PHP GD está habilitada. Se GD estiver indisponível, o envio continua com o código Pix copia-e-cola em texto. A biblioteca PHP vendorizada está em `app/Core/vendor/` e sua licença MIT acompanha o código.

Esta função não usa API Pix, não consulta o DICT e não confirma nem concilia pagamentos. O `txid` pode ajudar a localizar uma transferência, mas a identificação depende dos dados que o banco pagador disponibilizar. A confirmação continua sendo manual após análise do comprovante.

## Referências

- [Manual de Padrões para Iniciação do Pix, Banco Central do Brasil](https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Regulamento_Pix/II_ManualdePadroesparaIniciacaodoPix.pdf)
- [QRCode.js, repositório oficial e licença MIT](https://github.com/davidshimjs/qrcodejs)
- [Gerador QR Code PHP vendorizado, licença MIT](https://github.com/psyon/php-qrcode)
