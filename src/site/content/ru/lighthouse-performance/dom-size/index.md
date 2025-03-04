---
layout: post
title: Избегайте чрезмерного размера DOM
description: |2-

  Узнайте, как большой размер DOM может снизить производительность вашей веб-страницы и как можно уменьшить размер вашей модели DOM во время загрузки.
date: 2019-05-02
updated: 2019-10-04
web_lighthouse:
  - dom-size
tags:
  - memory
---

Большое дерево DOM может оказать множество негативных эффектов на производительность вашей страницы:

- **Эффективность сети и производительность загрузки**

    Большое дерево DOM часто включает в себя множество узлов, которые не видны при первой загрузке страницы пользователем, что излишне увеличивает расходы на передачу данных для ваших пользователей и замедляет время загрузки.

- **Производительность во время выполнения**

    Поскольку пользователи и скрипты взаимодействуют с вашей страницей, браузер должен постоянно [пересчитывать положение и стили узлов](https://developers.google.com/web/fundamentals/performance/rendering/reduce-the-scope-and-complexity-of-style-calculations?utm_source=lighthouse&utm_medium=cli). Большое дерево DOM в сочетании со сложными правилами стилей может сильно замедлить рендеринг.

- **Производительность памяти**

    Если ваш JavaScript использует общие селекторы запросов, такие как `document.querySelectorAll('li')`, вы можете неосознанно хранить ссылки на очень большое количество узлов, что может привести к перегрузке памяти устройств ваших пользователей.

## Причины плохих результатов аудита размера DOM в Lighthouse

[Lighthouse](https://developers.google.com/web/tools/lighthouse/) сообщает об общем количестве элементов DOM для страницы, максимальной глубине DOM и максимуме дочерних элементов:

<figure>{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/SUCUejhAE77m6k2WyI6D.png", alt="Снимок экрана аудита предотвращения чрезмерного размера DOM Lighthouse", width="800", height="363" %}</figure>

Lighthouse отмечает страницы со следующими деревьями DOM:

- Предупреждение при количестве узлов в элементе body более 800.
- Ошибка при количестве узлов в элементе body более 1400.

{% include 'content/lighthouse-performance/scoring.njk' %}

## Как оптимизировать размер DOM

В общем случае, ищите способы создавать узлы DOM только при необходимости и уничтожайте узлы, когда они больше не нужны.

Если на данный момент вы загружаете большое дерево DOM, попробуйте загрузить свою страницу и вручную отметить, какие узлы отображаются. Возможно, вы можете удалить неотображаемые узлы из первоначально загруженного документа и создавать их только после соответствующего взаимодействия с пользователем, такого как прокрутка или нажатие кнопки.

Если вы создаете узлы DOM во время выполнения, [точки прерывания при модификации поддерева DOM](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints#dom) могут помочь вам точно определить, когда создаются узлы.

Если избежать большого дерева DOM не удается, есть другой подход к повышению производительности рендеринга — упрощение ваших CSS-селекторов. Дополнительную информацию см. в документе Google «[Уменьшение объема и сложности вычислений стилей](https://developers.google.com/web/fundamentals/performance/rendering/reduce-the-scope-and-complexity-of-style-calculations)».

## Инструкции для разных стеков

### Angular

Если вы визуализируете большие списки, используйте [виртуальную прокрутку](/virtualize-lists-with-angular-cdk/) с помощью Component Dev Kit (CDK).

### React

- Используйте «оконную» библиотеку, такую как [`react-window`](/virtualize-long-lists-react-window/), чтобы минимизировать количество создаваемых узлов DOM, если вы визуализируете много повторяющихся элементов на странице.
- Сведите к минимуму ненужный повторный рендеринг, используя [`shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action), [`PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent) или [`React.memo`](https://reactjs.org/docs/react-api.html#reactmemo).
- Если вы используете хук `Effect` для повышения производительности во время выполнения, [пропускайте эффекты](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects) только до тех пор, пока не изменятся определенные зависимости.

## Ресурсы

- [Исходный код для аудита **предотвращения чрезмерного размера DOM**](https://github.com/GoogleChrome/lighthouse/blob/master/lighthouse-core/audits/dobetterweb/dom-size.js)
- [Уменьшение объема и сложности расчетов стилей](https://developers.google.com/web/fundamentals/performance/rendering/reduce-the-scope-and-complexity-of-style-calculations)
