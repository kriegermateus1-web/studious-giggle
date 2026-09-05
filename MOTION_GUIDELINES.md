# Motion Principles — Diretrizes de Movimento e Carregamento

Referência: https://github.com/kylezantos/design-principles

Objetivo
---------
Garantir que toda interface do sistema implemente comportamentos de carregamento e movimento bem definidos: skeletons, lazy-loading, animações de entrada/saída suaves, estados de carregamento e indicadores de progresso para elementos de UI. Estas regras são de cumprimento obrigatório para qualquer mudança de interface (frontend) no repositório.

Princípios gerais
------------------
- Consistência: padrões de skeletons, velocidades e easing devem ser consistentes entre componentes.
- Perceptual: animações devem ajudar o usuário a entender mudança de estado — não distrair.
- Performance: usar técnicas leves (CSS transforms, opacity) e lazy-loading para reduzir trabalho na main-thread.
- Acessibilidade: respeitar `prefers-reduced-motion`, garantir que indicadores de carregamento sejam anunciáveis (aria-busy/aria-live) e foco previsível.

Regras obrigatórias (Checklist por PR)
--------------------------------------
Para cada PR que altera UI, a descrição do PR deve incluir a seção "Motion Compliance" com a resposta a cada item e referências às linhas/arquivos relevantes.

Implementações mínimas obrigatórias:
1. Skeletons
   - Componentes que carregam dados assíncronos devem exibir skeletons de tamanho/forma similares ao conteúdo final.
   - O efeito de shimmer é recomendado, mas opcional; o importante é evitar jump layout.
   - Verificar se `aria-busy="true"` é aplicado ao contêiner durante o carregamento quando relevante.
2. Lazy-loading
   - Imagens e mídias fora da viewport devem usar `loading="lazy"` e/ou IntersectionObserver para carregamento progressivo.
   - Componentes pesados (widgets, iframes, vídeos) devem ser carregados sob demanda.
3. Animações de entrada / saída
   - Use transições baseadas em transform/opacity (ex.: translateY, scale, opacity) para melhor performance.
   - Durations e easings padronizados: recomenda-se 200–350ms para entradas, 120–200ms para saídas, easing cubic-bezier(.2,.8,.2,1) como padrão.
   - Sempre respeitar `prefers-reduced-motion` (desativar ou reduzir animações quando definido).
4. Estado de carregamento e progresso
   - Para operações longas (>= 300ms) apresentar indicador de progresso (linear ou circular) e/ou feedback percentual quando aplicável.
   - Usar skeletons para cargas curtas; usar uma barra de progresso global (ex.: NProgress) ou componente de progresso local para cargas maiores.
5. Performance e otimização
   - Evitar animações que provoquem reflows (top/left/width/height). Prefira transform e opacity.
   - Pré-carregar recursos críticos e usar placeholder de baixa qualidade (LQIP) quando possível.
6. Testes e validação
   - Testes manuais: passos para reproduzir e verificar (incluir no PR).
   - Se houver testes automatizados de UI (Cypress, Playwright), adicionar checks que confirmem a presença de skeletons / lazyload onde aplicável.

Snippets e exemplos (implementação recomendada)
------------------------------------------------
Skeleton básico (HTML + CSS):

```html name=example-skeleton.html
<div class="skeleton skeleton-avatar"></div>
<div class="skeleton skeleton-line"></div>
```

```css name=example-skeleton.css
.skeleton{background:linear-gradient(90deg,#eee 25%, #f5f5f5 37%, #eee 63%);background-size:400% 100%;animation:shimmer 1.2s linear infinite;border-radius:4px}
.skeleton-avatar{width:48px;height:48px;border-radius:50%}
.skeleton-line{height:14px;width:100%;margin-top:8px}
@keyframes shimmer{0%{background-position:200% 0}100%{background-position:-200% 0}}
@media (prefers-reduced-motion: reduce){.skeleton{animation:none;background:#eee}}
```

Lazy-loading de imagens com IntersectionObserver (fallback para `loading="lazy"`):

```javascript name=lazy-image.js
// Usar data-src em imagens; quando intersecta, set src e remove placeholder
const io = new IntersectionObserver((entries)=>{
  entries.forEach(entry=>{
    if(entry.isIntersecting){
      const img = entry.target;
      img.src = img.dataset.src;
      img.removeAttribute('data-src');
      io.unobserve(img);
    }
  })
},{rootMargin:'200px'});
document.querySelectorAll('img[data-src]').forEach(img => io.observe(img));
```

Entrada/saída suave com CSS (prefira classes utilitárias):
```css name=motion-utils.css
.fade-enter{opacity:0;transform:translateY(6px)}
.fade-enter-active{opacity:1;transform:none;transition:opacity .28s cubic-bezier(.2,.8,.2,1), transform .28s cubic-bezier(.2,.8,.2,1)}
.fade-exit{opacity:1}
.fade-exit-active{opacity:0;transition:opacity .18s cubic-bezier(.2,.8,.2,1)}
@media (prefers-reduced-motion: reduce){
  .fade-enter-active,.fade-exit-active{transition:none}
}
```

Barra de progresso global (ex.: NProgress) ou componente custom:
- Injetar um indicador de progresso quando rota muda ou quando o app inicia uma requisição global.
- Cancelar/encerrar indicador ao completar a requisição.

Acessibilidade
---------------
- Marcar contêineres com `aria-busy` durante carregamentos significativos.
- Usar `role="status"` com `aria-live="polite"` para mensagens não intrusivas.
- Respeitar `prefers-reduced-motion` e fornecer alternativa sem movimento.

Enforcement / Automação
------------------------
- PR template (obrigatório): incluir checklist “Motion Compliance” com as evidências.
- Criar linting/CI check leve (opcional): script que busca por imagens sem loading=lazy e components que carregam dados sem skeleton — ao menos avisar como warning.
- Code review: revisores devem confirmar compliance antes de merge para `main`.

Como proceder
--------------
1. Use este documento como referência para qualquer mudança de UI.
2. Ao abrir PRs que afetem UI, inclua a seção "Motion Compliance" com respostas claras e links para componentes/commits.
3. Posso criar Issues iniciais para aplicar essas melhorias ao site atual e abrir PRs para cada item (skeletons, lazy-loading, animações, progresso). Quer que eu crie essas Issues agora?

---

Arquivo gerado automaticamente por Copilot — adicionado ao repositório como MOTION_GUIDELINES.md
