# O Pastel Gigante - PDV

Sistema de PDV (Ponto de Venda) gratuito para pastelaria, rodando 100% no navegador via GitHub Pages. Sem servidor e sem custo de hospedagem.

Acesse: https://spyfer2-ux.github.io/pdv-pastelaria/

## Funcionalidades

- Atendimento por mesa (8 mesas). O cliente senta, pede e vai comendo; o pagamento fica para o final.
- Botao Cancelar Pedido: cancela o pedido caso o cliente desista, sem gerar venda.
- Remocao de item individual: no carrinho da mesa, cada item tem uma lixeira para remover so aquele item, sem precisar cancelar o pedido inteiro.
- Botao Deixar em Aberto (pagar depois): registra os itens na mesa e volta para as mesas, sem cobrar.
- Cardapio completo de pasteis salgados, incluindo as categorias Atum e Berinjela.
- Adicionais em todos os pasteis salgados (23 opcoes: mussarela, catupiry, bacon, carne, escarola, palmito, etc.).
- Cadastro de clientes: o botao Cadastrar Cliente salva o cliente (nome, telefone, endereco, observacao) de forma permanente na colecao clientes do Firebase, alem de vincular o cliente a venda atual. O formulario tem campos CEP, Endereco e Numero, com busca automatica de endereco por CEP (via API ViaCEP): ao digitar um CEP valido, os dados de logradouro, bairro, cidade e UF sao preenchidos sozinhos.
- Aviso de nova atualizacao: ao abrir o PDV, um modal central informa novidades ao usuario (ex.: a busca de endereco por CEP). Aparece uma unica vez por navegador (marcador pdv_update_cep_seen no localStorage).
- Integracao com o app do Franqueado (CANDEIA) via Firebase.
- Caixa obrigatorio (abertura e fechamento).

## Caixa obrigatorio (abertura e fechamento)

O sistema controla o caixa por dia:

- **Abertura:** ao abrir o sistema, aparece a tela ABERTURA DE CAIXA com a data do dia e a pergunta "Quanto tem de troco no caixa?". E obrigatorio digitar o valor do troco para comecar a usar.
- **Durante o dia:** se o caixa ja foi aberto hoje, o sistema abre normal, sem pedir de novo.
- **Fechamento:** quando abre em um novo dia com o caixa do dia anterior ainda aberto, aparece a tela FECHAMENTO DE CAIXA, mostrando o faturamento do dia e a conferencia da gaveta (troco inicial + vendas em dinheiro = valor esperado). Ha um campo para digitar quanto tem no caixa e ver a diferenca.
- Depois de fechar, o sistema reinicia e pede a abertura do novo dia.

Os dados do caixa ficam salvos no proprio navegador (localStorage), na chave opg_caixa.

## Cobrancas e vencimentos

Na tela Gerencial (Admin) ha o botao Cobrancas em Aberto, que lista todas as cobrancas:

- Cobrancas recorrentes fixas: Mensalidade do Sistema (R$ 119,00 - todo dia 15), Aluguel do Estabelecimento (R$ 1.200,00 - todo dia 24) e Royalties da Marca (R$ 1.874,00 - todo dia 15, com isencao ate 07/08/2026).
- Boletos avulsos em aberto (status pendente) cadastrados no app CANDEIA, lidos em tempo real da colecao boletos_avulsos do Firebase.

A lista mostra cada cobranca ordenada pela proximidade do vencimento, com destaque para "em atraso", "vence hoje" e "vence amanha".

### Pop-up de vencimento (1 dia antes)

Ao abrir o PDV, um pop-up automatico avisa sobre qualquer cobranca (recorrente ou avulsa) que vence em exatamente 1 dia. O pop-up so aparece uma vez por dia (controle via localStorage, chave pdv_venc_popup_visto).

## Sincronizacao do historico de vendas

As vendas sao gravadas localmente no navegador do aparelho (localStorage, chave opg_vendas) e tambem enviadas para a colecao pdv_vendas do Firebase, onde alimentam a aba Faturamento do app CANDEIA.

- Envio automatico: a cada venda finalizada, o PDV envia essa venda para a nuvem automaticamente (funcao registrarVendaMesa -> addDoc na colecao pdv_vendas). Nao e preciso atualizar manualmente todo dia. O envio depende de conexao com a internet e da regra de seguranca do Firestore permitir a escrita em pdv_vendas (ver secao Regras do Firestore).
- Envio manual (reforco): na tela Gerencial, o botao Atualizar Sistema (e o pop-up "Atualizacao disponivel" com o botao Atualizar agora) dispara o envio em lote de todas as vendas locais. Util para reenviar vendas antigas ou vendas que ficaram so no aparelho caso tenha faltado internet no momento.

### Restaurar vendas da nuvem

Existe a funcao restaurarVendasNuvem() que baixa todas as vendas da colecao pdv_vendas do Firebase, converte para o formato local e substitui o historico do aparelho (opg_vendas), recarregando a pagina em seguida.

- Serve para recuperar o historico em um aparelho novo ou que perdeu os dados locais.
- Atencao: ela substitui o historico local pelo da nuvem, entao nao deve ser usada em um aparelho que tenha vendas recentes ainda nao sincronizadas.
- Hoje ela e disparada pelo Console do navegador (F12) digitando restaurarVendasNuvem().

Importante: o historico local fica no aparelho onde o PDV roda. O envio manual em lote precisa ser feito nesse mesmo aparelho.

## Regras do Firestore

Para o envio e a leitura das vendas funcionarem, a colecao pdv_vendas precisa estar liberada nas Regras de Seguranca do Firestore (projeto candeia-jr). O cadastro de clientes tambem grava na colecao clientes, que precisa estar liberada. Regras atuais:

    match /pdv_vendas/{docId} {
      allow read, write: if true;
    }

    match /clientes/{docId} {
      allow read, write: if true;
    }

Observacao de seguranca: com if true as colecoes pdv_vendas e clientes ficam publicas (qualquer um com a URL do projeto pode ler/gravar). No caso de clientes isso expoe dados pessoais (nome, telefone, endereco). Recomenda-se migrar futuramente para autenticacao (ex.: login anonimo do Firebase + allow read, write: if request.auth != null).

## Tecnologia

- HTML, CSS e JavaScript em um unico arquivo (index.html).
- Firebase / Firestore (projeto candeia-jr) para sincronizar as vendas com o app do Franqueado (CANDEIA).
- Hospedagem gratuita no GitHub Pages.

### Colecoes do Firebase usadas

- pdv_vendas: vendas do PDV (campos: timestamp, data em ISO, estab, total, itens, pagamentos).
- clientes: clientes cadastrados no PDV (campos: nome, tel, endereco, obs, criadoEm). O campo endereco e um texto unico montado a partir dos campos Endereco, Numero e CEP do formulario.
- boletos_avulsos: cobrancas avulsas cadastradas no CANDEIA (lidas para o pop-up e a lista de cobrancas).

### Chaves de localStorage

- opg_produtos, opg_mesas, opg_adicionais, opg_caixa: estado operacional do PDV.
- opg_vendas: historico de vendas do aparelho.
- opg_historico_migrado: marca que o historico ja foi sincronizado.
- pdv_venc_popup_visto: controla o pop-up de vencimentos (1x por dia).

## Como usar

1. Abra o sistema no navegador (celular, tablet ou computador).
2. Faca a abertura de caixa informando o troco.
3. Selecione a mesa e lance os pedidos.
4. Use Deixar em Aberto para mesas que pagam no final, ou finalize a venda.
5. No dia seguinte, faca o fechamento e confira a gaveta.
