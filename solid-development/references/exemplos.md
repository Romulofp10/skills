# Exemplos e anti-padrões por princípio

## SRP
**Anti-padrão:** `UserService` que valida, grava no banco, envia e-mail de boas-vindas e gera relatório.
**Refatoração:** `UserValidator`, `UserRepository`, `WelcomeEmailSender`, `UserReportGenerator`, orquestrados por um caso de uso `RegisterUser`.

## OCP
**Anti-padrão:**
```ts
function discount(type: string, total: number) {
  switch (type) {
    case "regular": return total;
    case "vip": return total * 0.9;
    case "black": return total * 0.8;
  }
}
```
**Refatoração:**
```ts
interface DiscountPolicy { apply(total: number): number; }
class VipDiscount implements DiscountPolicy { apply(t: number) { return t * 0.9; } }
class BlackDiscount implements DiscountPolicy { apply(t: number) { return t * 0.8; } }

const policies: Record<string, DiscountPolicy> = {
  vip: new VipDiscount(),
  black: new BlackDiscount(),
};
```

## LSP
**Anti-padrão:** `Square extends Rectangle` alterando `setWidth` para também mudar a altura, quebrando quem espera independência entre os lados.
**Refatoração:** abstração `Shape { area(): number }` com `Rectangle` e `Square` independentes.

**Anti-padrão:** `ReadOnlyRepository extends Repository` lançando erro em `save()`.
**Refatoração:** separar `ReadRepository` e `WriteRepository`.

## ISP
**Anti-padrão:**
```ts
interface Worker { work(): void; eat(): void; }
class Robot implements Worker { work() {} eat() { throw new Error("N/A"); } }
```
**Refatoração:** `Workable { work() }` e `Feedable { eat() }`.

## DIP
**Anti-padrão:** caso de uso importando `PrismaClient` ou `axios` diretamente.
**Refatoração:** o caso de uso depende de `OrderRepository` / `PaymentGateway` (interfaces no domínio); `PrismaOrderRepository` e `StripePaymentGateway` vivem na infraestrutura e são ligados no composition root.

```ts
// composition root
const repo = new PrismaOrderRepository(prisma);
const notifier = new EmailNotifier(mailer);
const createOrder = new CreateOrder(repo, notifier);
```

## Smells que indicam violação
| Smell | Princípio provável |
|---|---|
| Classe com mais de ~300 linhas ou muitos métodos não relacionados | SRP |
| `switch`/`if-else` por tipo repetido em vários lugares | OCP |
| `instanceof` no chamador, `throw NotSupported` em override | LSP |
| Métodos vazios em implementações | ISP |
| `new` de serviços concretos dentro da regra de negócio | DIP |
| Testes exigindo banco, rede ou muitos mocks | SRP / DIP |


## Preferencias do usuario.
- Sempre antes de começar a ler o codigo voce ira verificar o resumo dele , antes de aplicar qualquer coisa para saber do que se trata, quando for uma janela de context nova.

## Preferencias front-end
- toda vez que for trazer da api uma quantidade de dados utilize a paginacao do projeto e prepare para o recebimento dela.
- Comentarios em cima do componente  informando o que faz e nomes de componentes order-component. order-item-list
- Evite a utilizacao de unkwnow ou any. efetue uma pasta de tipagem de acordo com a entidade e tenha la a preparacao do payload e do response separadas. ou seja o contrato.
- so colicar tipagem dentro do componente quando for algo especifico dele e que nao utilize em outros lugares, para que seja reaproveitado.
- Quando for api que for trazer uma listagem de dados para o front-end trabalhe com paginacao.
