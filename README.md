# Michelle Oficial — deploy na Vercel

Projeto em Vite/TanStack Start. Para executar localmente, use `npm install` e `npm run dev`.

## Integração TichuPay

O backend cria cobranças Pix em `POST https://nexuspag.com/api/pix/create`, autenticando com o header `x-api-key`. O status é consultado em `GET https://nexuspag.com/api/pix/{id}` (o `{id}` pode ser o UUID da transação, o `txid` ou o `external_id`). O código Pix copia-e-cola e o QR Code vêm em `transaction.pix_copia_cola` e `transaction.qr_code_base64`. Documentação oficial: https://tichupay.com/docs

Configure em **Vercel → Project → Settings → Environment Variables** (e faça um **Redeploy** depois):

- `TICHUPAY_API_KEY` (obrigatória): chave de API do painel da TichuPay → Integrações → API Keys.
- `TICHUPAY_WEBHOOK_SECRET` (opcional): segredo de webhook, se configurado no painel.
- `APP_BASE_URL` (opcional): URL HTTPS pública do projeto, por exemplo `https://seu-projeto.vercel.app`. Se definida, a cobrança envia `webhook_url` apontando para `/api/webhooks/tichupay`.
- `TICHUPAY_API_BASE` (opcional): só altere se a TichuPay informar outra URL base. O padrão é `https://nexuspag.com`.

Cadastre credenciais reais diretamente na Vercel. Não as coloque no GitHub, no frontend ou em variáveis com prefixo `VITE_`. Pode apagar as variáveis antigas (`SMOKEPAY_*`, `OMEGAPAY_*`, `ATENASPAY_*`) depois de confirmar que o deploy novo funciona.

O checkout Pix não pede nome, e-mail, CPF ou telefone. O `external_id` enviado à TichuPay é a chave de idempotência da tentativa somada ao valor em centavos: repetir o mesmo pedido não duplica a cobrança, e mudar o valor (por exemplo, ao adicionar um extra) gera uma cobrança nova com o valor correto. O site consulta o status periodicamente e só libera o acesso quando o status retorna `paid`. Valide com uma cobrança de baixo valor antes de liberar vendas reais.
