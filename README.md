# Atividade-09-03
 -----------------------------------------PARTE 1-------------------------------------
 A classe ProcessadorPedido tem 3 responsabilidades diferentes:

1. calcularTotal -> calcula o imposto dependendo do tipo de produto
    Quem pediria mudança: setor fiscal/contabilidade
 2. processarPagamento -> processa o pagamento dependendo da forma
    Quem pediria mudança: setor financeiro

 3. enviarConfirmacao -> envia email pro cliente
    Quem pediria mudança: marketing/TI

 Violações do OCP:
  calcularTotal usa switch(tipoProduto), ou seja, se aparecer um novo
  tipo de produto precisa abrir essa classe e editar
  processarPagamento usa switch(formaPagamento), mesma coisa,
  qualquer novo método de pagamento obriga a editar a classe


----------------------------------PARTE 2-------------------------------


Interfaces
interface CalculoImposto {
  calcular(preco: number, quantidade: number): number;
}

interface ProcessadorPagamento {
  processar(valor: number): void;
}

class Pedido {
  constructor(
    public tipoProduto: string,
    public preco: number,
    public quantidade: number,
    public emailCliente: string
  ) {}
}

class ImpostoEletronico implements CalculoImposto {
  calcular(preco: number, quantidade: number): number {
    const subtotal = preco * quantidade;
    return subtotal + subtotal * 0.15; // 15%
  }
}

class ImpostoAlimento implements CalculoImposto {
  calcular(preco: number, quantidade: number): number {
    const subtotal = preco * quantidade;
    return subtotal + subtotal * 0.05; // 5%
  }
}

class ImpostoVestuario implements CalculoImposto {
  calcular(preco: number, quantidade: number): number {
    const subtotal = preco * quantidade;
    return subtotal + subtotal * 0.10; // 10%
  }
}

class PagamentoCartao implements ProcessadorPagamento {
  processar(valor: number): void {
    console.log(`Processando cartão de crédito: R$ ${valor.toFixed(2)}`);
  }
}

class PagamentoBoleto implements ProcessadorPagamento {
  processar(valor: number): void {
    console.log(`Gerando boleto: R$ ${valor.toFixed(2)}`);
  }
}

class PagamentoPix implements ProcessadorPagamento {
  processar(valor: number): void {
    console.log(`Gerando QR Code PIX: R$ ${valor.toFixed(2)}`);
  }
}

class ConfirmacaoEmail {
  enviar(email: string, valor: number): void {
    console.log(`Email enviado para ${email}: pedido de R$ ${valor.toFixed(2)} confirmado!`);
  }
}

class PedidoService {
  private confirmacao = new ConfirmacaoEmail();

  processarPedido(
    pedido: Pedido,
    imposto: CalculoImposto,
    pagamento: ProcessadorPagamento
  ): void {
    console.log(`\n-- Pedido: ${pedido.tipoProduto} --`);

    const total = imposto.calcular(pedido.preco, pedido.quantidade);
    console.log(`Total: R$ ${total.toFixed(2)}`);

    pagamento.processar(total);
    this.confirmacao.enviar(pedido.emailCliente, total);
  }
}

const service = new PedidoService();

const pedido1 = new Pedido("ELETRONICO", 1000, 2, "email da pessoa");
service.processarPedido(pedido1, new ImpostoEletronico(), new PagamentoCartao());

const pedido2 = new Pedido("ALIMENTO", 50, 3, "email da pessoa");
service.processarPedido(pedido2, new ImpostoAlimento(), new PagamentoPix());

const pedido3 = new Pedido("VESTUARIO", 200, 1, "email da pessoa");
service.processarPedido(pedido3, new ImpostoVestuario(), new PagamentoBoleto());
