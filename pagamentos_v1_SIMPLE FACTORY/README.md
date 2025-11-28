Arquivo pagamento.

Python
from abc import ABC, abstractmethod

# Hierarquia de pagamentos

class Pagamento(ABC):
    @abstractmethod
    def pagar(self, valor: float) -> None:
        pass
1. Importações e Interface Abstrata
from abc import ABC, abstractmethod:

Essas linhas importam as ferramentas necessárias para criar Classes Abstratas em Python.

ABC (Abstract Base Class) sinaliza que a classe Pagamento não pode ser instanciada diretamente.

class Pagamento(ABC)::

Define a classe base Pagamento, que herda de ABC.

@abstractmethod:

Este decorador, aplicado ao método pagar, garante que toda e qualquer subclasse de Pagamento deve obrigatoriamente fornecer sua própria implementação do método pagar().

def pagar(self, valor: float) -> None:

Define a assinatura do método que todas as formas de pagamento compartilharão.

Arquivo factory.

1. Importações e Definição da Fábrica
Python

from pagamento import Pagamento, PagamentoCartao, PagamentoBoleto, PagamentoPix

class PagamentoFactory:
A primeira linha importa a Interface do Produto (Pagamento – a classe abstrata) e todos os Produtos Concretos (PagamentoCartao, PagamentoBoleto, etc.). A fábrica precisa conhecer todos os objetos que pode criar.

A classe PagamentoFactory é a própria Fábrica Simples. Ela terá o método que o cliente chamará para pedir um objeto.

2. O Método de Fábrica (criarPagamento)
Python

    def criarPagamento(self, canal: str, tipo: str) -> Pagamento:
        """Cria instância de Pagamento com base no canal e tipo."""
        # ... lógica condicional de criação ...
Assinatura do Método: Este método recebe informações (canal e tipo) e, com base nelas, decide qual objeto criar.

Tipo de Retorno (-> Pagamento): Este é o ponto mais crucial. Embora o método crie e retorne uma instância de uma classe concreta (como PagamentoPix), ele garante que o tipo retornado seja sempre a interface abstrata Pagamento.

A Lógica Condicional Centralizada
Todo o bloco if/elif/else dentro do método é a lógica que decide qual classe instanciar:

Python

        if canal == "online":
            # ...
            if tipo == "pix":
                return PagamentoPix() # Onde a instanciação ocorre
            # ...
        elif canal == "offline":
            # ...
            if tipo == "boleto":
                return PagamentoBoleto() # Mais instanciação
        # ...
Encapsulamento: As chamadas para os construtores (PagamentoPix(), PagamentoBoleto()) estão escondidas dentro da Fábrica. Se as regras de criação ou os nomes das classes mudarem, você só precisa alterar este método, e não o código cliente.

Validação: O método também trata de casos não suportados, levantando exceções (ValueError), garantindo que a fábrica não retorne objetos inválidos.

Arquivo main.

1. O Princípio de Programar para a Interface
Anteriormente, você perguntou: "Por que é fundamental que criarPagamento retorne a interface abstrata (Pagamento)?"

A resposta está claramente demonstrada na função realizar_pagamento:

Python

def realizar_pagamento(canal: str, tipo: str, valor: float):
    # 1. O cliente interage APENAS com a Fábrica.
    factory = PagamentoFactory()
    
    # 2. A Fábrica decide qual objeto concreto criar.
    pagamento = factory.criarPagamento(canal, tipo) 
    
    # 3. O cliente usa o objeto retornado (o Produto).
    pagamento.pagar(valor) 
Observe a linha pagamento.pagar(valor). O código cliente não sabe e não se importa se pagamento é uma instância de PagamentoPix, PagamentoBoleto ou PagamentoCartao.

O código só precisa saber que o objeto retornado é um Pagamento (a interface) e, portanto, garante que ele possui o método pagar().

Questão-Guia (Reforço)
Por que dizemos que o acoplamento (dependência) do código cliente com as classes concretas (PagamentoPix, etc.) é considerado baixo ou fraco quando usamos este padrão?

2. Flexibilidade e Manutenibilidade
Anteriormente, você também perguntou: "Qual o principal benefício de ter a lógica de if/elif concentrada dentro da fábrica?"

A resposta está relacionada à manutenibilidade e ao Princípio do Aberto/Fechado (Open-Closed Principle).

O código está aberto para extensão (podemos adicionar novas classes de pagamento).

O código está fechado para modificação (a função realizar_pagamento e todo o código cliente não precisa ser alterado).

Exemplo de Uso (if __name__ == "__main__":)
Python

if __name__ == "__main__":
    realizar_pagamento("online", "pix", 120.0)      # PIX online
    realizar_pagamento("offline", "boleto", 500.0)   # Boleto offline
    # ...
Cada chamada a realizar_pagamento é a prova de que o cliente trata todas as formas de pagamento da mesma maneira, através da interface comum.