# 💰 Plano de Monetização: Nora e Vaso

## **1. Modelo de Negócio (SaaS Recorrente)**

Sua plataforma segue o modelo **Software as a Service (SaaS)** com cobrança recorrente.

### Opção A: Assinatura Mensal (Recomendado)

```
Plano Gratuito (Free Trial)
├─ 7 dias de acesso completo
├─ Sem cartão de crédito
└─ Propósito: converter em pago

Plano Básico: R$ 49/mês
├─ Acesso a todos os 9 tópicos
├─ Até 5 usuários (ideal para pequenas UTIs)
├─ Suporte por email
└─ Certificado de conclusão

Plano Profissional: R$ 149/mês
├─ Usuários ilimitados
├─ Acesso prioritário a novo conteúdo
├─ Relatórios personalizados
├─ Suporte por chat (8h-18h)
└─ Síncronização com 3 dispositivos

Plano Institucional: R$ 499/mês
├─ Ilimitado para hospital/rede
├─ Acesso administrativo avançado
├─ Integração com seus sistemas
├─ Suporte 24/7
├─ Conteúdo customizado
└─ Reunião trimestral com seu time
```

### Opção B: Pagamento Único (Complementar)

```
Acesso Vitalício: R$ 1.999 (paga uma vez)
├─ Permanente (sem cancelamento)
├─ Ideal para profissionais liberais
└─ Menos recorrência, mas +receita imediata
```

---

## **2. Mercado-alvo**

### Segmento 1: Médicos Residentes
- **Tamanho:** ~5.000 residentes em SP
- **Disposição a pagar:** R$ 30-50/mês
- **Estratégia:** Plano Básico com desconto (R$ 29/mês)

### Segmento 2: Hospitais Pequenos/Médios
- **Tamanho:** ~200 hospitais em SP
- **Disposição a pagar:** R$ 1.000-3.000/mês
- **Estratégia:** Plano Institucional (5-20 usuários)

### Segmento 3: Redes de Saúde
- **Tamanho:** ~20 grandes redes em SP
- **Disposição a pagar:** R$ 5.000-20.000/mês
- **Estratégia:** Custom enterprise

### Segmento 4: Livrarias Online / Udemy
- **Modelo:** Revenda (eles cobram 30%, você fica 70%)
- **Potencial:** R$ 200-500/venda

---

## **3. Implementar Pagamento com Stripe** (2 semanas)

### Passo 1: Criar conta Stripe

1. Vá para **https://stripe.com/br**
2. Clique em **"Comece agora"**
3. Preencha dados da sua empresa
4. Confira a conta bancária (receberá aqui)
5. Copie sua **Chave Pública** e **Chave Privada**

### Passo 2: Adicionar Stripe ao seu código

```javascript
// No seu HTML, antes de </head>, adicione:
<script src="https://js.stripe.com/v3/"></script>

// Dentro do seu código React, crie uma função:
const handleCheckout = async (planId) => {
  const response = await fetch('seu-servidor/create-checkout-session', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ planId, userId: user.id })
  });

  const { sessionId } = await response.json();
  
  // Redirecionar para checkout
  const stripe = window.Stripe('sua-chave-publica');
  stripe.redirectToCheckout({ sessionId });
};
```

### Passo 3: Backend Node.js + Stripe (no seu servidor)

```javascript
// servidor.js
const express = require('express');
const stripe = require('stripe')('sua-chave-privada');

app.post('/create-checkout-session', async (req, res) => {
  const { planId, userId } = req.body;

  const plans = {
    basic: { price: 'price_xxxxx', name: 'Básico' },
    pro: { price: 'price_yyyyy', name: 'Profissional' },
    enterprise: { price: 'price_zzzzz', name: 'Institucional' }
  };

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{
      price: plans[planId].price,
      quantity: 1
    }],
    mode: 'subscription', // recorrente
    success_url: 'https://noraevaso.com.br/obrigado',
    cancel_url: 'https://noraevaso.com.br/planos',
    customer_email: req.body.email,
    metadata: { userId }
  });

  res.json({ sessionId: session.id });
});
```

---

## **4. Projeção de Receita (12 meses)**

### Cenário Conservador

```
Mês 1-3: Fase de lançamento
├─ 10 usuários pagos (R$ 49) = R$ 490/mês
├─ 1 hospital (R$ 499) = R$ 499/mês
└─ Receita: ~R$ 1.000/mês

Mês 4-6: Growth inicial
├─ 30 usuários × R$ 49 = R$ 1.470/mês
├─ 3 hospitais × R$ 499 = R$ 1.497/mês
└─ Receita: ~R$ 3.000/mês

Mês 7-9: Escala
├─ 80 usuários × R$ 49 = R$ 3.920/mês
├─ 8 hospitais × R$ 499 = R$ 3.992/mês
└─ Receita: ~R$ 8.000/mês

Mês 10-12: Aceleração
├─ 150 usuários × R$ 49 = R$ 7.350/mês
├─ 15 hospitais × R$ 499 = R$ 7.485/mês
├─ 2 redes × R$ 2.500 = R$ 5.000/mês
└─ Receita: ~R$ 20.000/mês

TOTAL ANUAL: ~R$ 115.000
```

### Cenário Otimista

```
COM MARKETING AGRESSIVO:
├─ Mês 12: R$ 50.000/mês
├─ TOTAL ANUAL: ~R$ 300.000
└─ Churn rate: 5% (mantém cliente)
```

---

## **5. Estratégia de Aquisição (Marketing)**

### Fase 1: Gratuito (Mês 1-3)

✅ **Conteúdo Free (40% dos tópicos)**
- Atrair tráfego orgânico
- Nutrição de leads via email

✅ **SEO**
- Blog posts: "Como diagnosticar HIC em 5 minutos"
- YouTube: Resumos dos tópicos

✅ **Networking**
- Enviar convite para residentes de UTI
- Parcerias com simuladores médicos

### Fase 2: Trial (Mês 4-6)

✅ **Trial de 7 dias**
- Sem cartão de crédito (núcleo duro)
- Onboarding automático por email

✅ **Referral**
- Desconto para quem indica amigos
- "Indique 3 e ganhe 1 mês grátis"

✅ **Parcerias com Hospitais**
- Oferecer desconto para toda equipe
- Proposta: "Treinamento de residentes em 1 plataforma"

### Fase 3: Paid (Mês 7+)

✅ **Ads em rede médica**
- Google Ads: "Curso medicina intensiva"
- Facebook/Instagram para residentes

✅ **Webinários gratuitos**
- "Novidades em protocolos de choque"
- Gated (email necessário)

✅ **Parcerias B2B**
- Convencer hospitais a oferecer aos residentes
- Cobrar do hospital (50%) e residentes com desconto (50%)

---

## **6. Redução de Custos**

### Infraestrutura

```
Supabase: 
└─ Free: até 500MB (Grátis)
└─ Pro: R$ 500/mês (para 10k+ usuários)

Vercel:
└─ Free: ilimitado para sites estáticos
└─ Pro: R$ 70/mês (builds avançados)

Email (SendGrid):
└─ 100/dia grátis
└─ R$ 100/mês para 100k emails

TOTAL INICIAL: ~R$ 0 (freeware)
TOTAL ESCALA: ~R$ 670/mês (< 5% da receita)
```

---

## **7. Checklist de Implementação**

### Semana 1: Setup Stripe

- [ ] Conta Stripe criada
- [ ] Produtos/Preços cadastrados
- [ ] Webhook configurado
- [ ] Página de planos criada

### Semana 2-3: Backend

- [ ] Node.js + Stripe integrado
- [ ] Webhook para ativar/desativar acesso
- [ ] Email de confirmação de pagamento

### Semana 4: Frontend

- [ ] Botão "Upgrade" na dashboard
- [ ] Checkout Stripe integrado
- [ ] Página de sucesso

### Mês 2: Marketing

- [ ] Conteúdo free no site
- [ ] Trial de 7 dias ativo
- [ ] Email de onboarding automático

### Mês 3: Growth

- [ ] Ads no Google (R$ 500/mês)
- [ ] Parcerias com hospitais
- [ ] Primeira campanha de referral

---

## **8. KPIs para Acompanhar**

```
Growth:
├─ MRR (Monthly Recurring Revenue) = meta R$ 50k/ano
├─ CAC (Customer Acquisition Cost) = quanto gasta por cliente
└─ LTV (Lifetime Value) = quanto cada cliente vale

Retenção:
├─ Churn rate = quantos cancelam/mês (meta: < 5%)
└─ Retention = quantos permanecem (meta: > 90%)

Produto:
├─ Engagement = % que usa a plataforma
├─ NPS = satisfação do cliente (meta: > 60)
└─ Bugs/Feedback por semana
```

---

## **9. Próximas Features (Alto ROI)**

```
[Impacto Alto]
├─ Certificado digital (aumenta LTV)
├─ Integração com WhatsApp (melhor retenção)
└─ API para outros hospitais (novo mercado)

[Impacto Médio]
├─ Analytics para hospitais (melhor upgrade)
├─ Modo offline (mais uso)
└─ Aplicativo mobile (mais tráfego)

[Impacto Baixo]
├─ Dark mode
├─ Gamification
└─ Community forum
```

---

## **10. Estrutura de Receita Final**

```
Receita Mensal (Cenário Realista - Mês 12)

Indivíduos (150 @ R$49)
└─ R$ 7.350

Pequenos Hospitais (10 @ R$499)
└─ R$ 4.990

Redes de Saúde (2 @ R$2.500)
└─ R$ 5.000

Pré-vendas Vitalício (5 @ R$1.999)
└─ R$ 9.995 (única vez)

─────────────────────
TOTAL: R$ 27.335/mês
       R$ 328.020/ano
```

---

**Você tem um modelo de negócio validado e pronto para escalar! 🚀**

Próximo passo: Implementar Stripe e começar a vender.
