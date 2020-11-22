# Solid 

Na programação de computadores orientada a objetos, o termo SOLID é um acrônimo para cinco postulados de design, destinados a facilitar a compreensão, o desenvolvimento e a manutenção de software.

Os postulados SOLID não devem ser confundidos com as orientações conhecidas como GRASP.

De fato, os postulados SOLID foram apresentados por Robert C. Martin em um artigo publicado no ano 2000 cujo título, em tradução livre, é "Postulados de Projeto e Padrões de Projeto". O acrônimo SOLID propriamente dito teria sido cunhado mais tarde por Michael Feathers.

# Princípio de única responsabilidade[1]
"uma classe deve ter apenas uma única responsabilidade (mudanças em apenas uma parte da especificação do software, devem ser capaz de afetar a especificação da classe)."
SRP - Single responsibility principle
Princípio da Responsabilidade Única - Uma classe deve ter um, e somente um, motivo para mudar.

Cada responsabilidade deve ser uma classe e cada classe deve ter uma única responsabilidade. Vejamos a classe abaixo (errada):
```JS 
public class GerarNotaFiscal{
	public void ValidarDadosDoCliente(){ ... }
	public void GerarImpostoFiscal(decimal ValorVenda){ ... }
	public void SalvarNotaFiscal(decimal ValorFinal) { ... }
	public void ImprimirCupomFiscal() { ... }
	public void EnviarCupomPorEmail() { ... }
}
```
- Apesar de compilar e rodar, tem uma série de responsabilidades estas que não são inerentes a ela:
- Validar dados do Cliente;
- Gerar Imposto;
- Salvar Cupom Fiscal;
- Imprimir Cupom Fiscal;
- Enviar Cupom por e-mail;

Vamos supor que o método ValidarDadosDoCliente() necessitasse ser alterado e após a alteração, um bug não identificado tenha sido publicado em produção.
Teríamos um cenário drástico, pois toda a estrutura da classe foi comprometida, ou seja, o sistema deixaria de emitir notas fiscais por conta de um bug na validação dos dados do cliente.
Além de gerar problemas graves, este tipo de acoplamento traz complexidade ao desenvolver testes além de deixa-los menos eficazes.
Quando se trabalha com o tipo de arquitetura acima, tem-se a falsa impressão de que o sistema está sendo construído de forma prática e simples, pois se tem poucas classes para se dar manutenção, mas, quando elas começam a se expandir, é que vemos como o sistema ficou muito mais complexo e de difícil manutenção.
Quando trabalhamos com SRP tem-se o sentimento de estarmos criando muitas classes para pouco uso, mas quando as funcionalidades do sistema crescem, podemos perceber que a expansão nada mais é do que criar novas classes nos lugares corretos e conecta-las de forma coesa.
```JS
public class DadosDoCliente{
	Validar();
}

public class ImpostosCupomFiscal{
	GerarImpostos(decimal ValorVenda);
}

public class PersistenciaCupomFiscal{
	Salvar (decimal ValorFinal);
}

public class EmissaoCupomFiscal{
	Imprimir();
}

public class ComunicacaoCupomFiscal{
	EnviarPorEmail();
}   
```
Facilidade de manutenção e evolução do código;
Código limpo e de fácil entendimento;
Facilidade para desenvolvimento de testes;
Redução do acoplamento;
Complexidade reduzida;
Coesão das classes;
Vendo a enorme quantidade de benefícios que tivemos com um simples refactoring, não temos como negar a necessidade de aplicarmos o SRP em nossas aplicações.


# Princípio de aberto/fechado[2]
"entidades de software devem ser abertas para extensão, mas fechadas para modificação."
OCP - Open/closed principle
Princípio do Aberto/Fechado - Você deve ser capaz de estender um comportamento de uma classe sem a necessidade de modificá-lo.
Em outras palavras significa que esta classe pode ter seu comportamento alterado com facilidade quando necessário, sem a alteração do seu código fonte. Essa extensão pode ser feita através de herança, interface e composição.

exemplo

```JS
public enum TipoEmail {
	Texto,
	Html,
	Criptografado
}

public void class EnviarEmail(string mensagem, string assunto, TipoEmail tipo){
	if(tipo == TipoEmail.Texto)
	{
		mensagem = this.RemoverFormatacao(mensagem);
	}
	else if(tipo == TipoEmail.Html)
	{
		mensagem = this.InserirHtml(mensagem); 
	} 
	else if(tipo == TipoEmail.Criptografado)
	{
		mensagem = this.CriptografarMensagem(mensagem);
	}

	this.EnviarMensagem();
}
```
Acima temos o exemplo de uma classe que valida o tipo de e-mail para tratar a mensagem de acordo com o seu tipo, mas, quando uma nova forma de mensagem for criada, a classe deverá ser editada e um novo if deverá ser acrescentado a ela.

O conceito de aberto/fechado (OCP) tem a premissa de criarmos novas classes para funcionalidades de tipos semelhantes, e caso tenhamos novas funcionalidades, novas classes sejam criadas.

o que ganhamos aplicando o OCP?
Extensibilidade
Como citei, ao termos uma nova funcionalidade ou comportamento, não precisaremos alterar a classe já existente, e sim estendê-la. Com isso mantemos o código original confiável e intacto, e criamos um código com design duradouro, de qualidade e manutenibilidade altas.
Abstração
Com toda certeza já ouvimos sobre abstração, ela permite que toda a mágica aconteça. Se temos abstrações bem definidas, conseguimos de forma fácil estender os métodos da nossa aplicação.
O conceito OCP indica principalmente o uso da herança para praticarmos o extensão dos métodos.

Como a classe deveria ficar?

```JS
public abstract class Email
{
	public abstract void Enviar(string assunto, string mensagem);
}

public class TextoEmail : Email
{
	public override void Enviar(string assunto, string mensagem)
	{
		Util.RemoverFormatacao(mensagem);
	}
}

public class HtmlEmail : Email
{
	public override void Enviar(string assunto, string mensagem)
	{
  		Util.InserirHtml(mensagem);
	}
}

public class CriptografadoEmail : Email
{
	public override void Enviar(string assunto, string mensagem)
	{
		Util.CriptografarMensagem(mensagem);;
	}
}
```

Veja que na solução, criamos várias classes, cada uma com uma responsabilidade definida, suas próprias regras de negócios e sem a necessidade de alterarmos a funcionalidade padrão devido a criação de uma nova regra.
Este design é a base de muitos dos padrões de projetos, um exemplo é o Factory Method, onde as classes de negócio estão implementadas sem uma especificação da classe concreta.


# Princípio da substituição de Liskov
"objetos em um programa devem ser substituíveis por instâncias de seus subtipos, sem alterar a funcionalidade do programa." deve ser capaz de afetar apenas a especificação da classe
Princípio da substituição de Liskov - As classes derivadas devem ser substituíveis por suas classes bases.


# Princípio da segregação de Interface[3]
"muitas interfaces de clientes específicas, são melhores do que uma para todos propósitos."
ISP - Interface segregation principle
Princípio da segregação de interfaces - Muitas interfaces específicas são melhores do que uma interface única geral.

# Princípio da inversão de dependência [4]
"deve-se depender de abstrações, não de objetos concretos."
DIP - Dependency inversion principle
Princípio da inversão de dependência - Dependa de abstrações e não de implementações.


- Desvantagens em não usar 


Não utilizar SOLID significa expor sua aplicação a problemas como:
Repetição de Código, ou seja, uma simples alteração deve ser replicada em vários pontos diferentes da sua aplicação;
Código sem estrutura coesa ou padronizada;
Rigidez e fragilidade, ou seja, qualquer alteração provoca uma cascata de operações ou falhas em várias partes do sistema;
Dificuldade na execução e criação de testes;
Não reaproveitamento, ou seja, nenhuma ou quase nenhuma funcionalidade pode ser reaproveitada para outros sistemas.

- Realmente, muitos pontos negativos, e quais são os positivos?

Aplicando os princípios adquirimos para a nossa aplicação benefícios da orientação a objetos, tais como:
Fácil manutenção, entendimento e organização;
Arquitetura aberta a receber atualizações, melhorias e novos recursos sem danos colaterais;
Aplicação de testes de forma fácil e de simples entendimento;
Fácil reaproveitamento de código;
Fácil adaptação a mudanças no escopo do projeto.

