---
title: Introducción a Servicios de Azure AI
permalink: index.html
layout: home
---

Los siguientes ejercicios se han diseñado para brindarte una experiencia de aprendizaje práctica en la que explorarás las tareas comunes que realizan los desarrolladores al crear soluciones de IA generativa en Microsoft Azure.

> **Nota**: para completar los ejercicios, necesitarás una suscripción a Azure en la que tengas permisos y cuota suficientes para aprovisionar los recursos de Azure y modelos de IA generativa necesarios. Si aún no tienes una, puedes registrarte para obtener una [cuenta de Azure](https://azure.microsoft.com/free). Hay una opción de evaluación gratuita para los nuevos usuarios que incluye créditos durante los primeros 30 días.

## Ejercicios

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {{activity.lab.description}} {% endfor %}

> **Nota**: aunque puedes completar estos ejercicios por tu cuenta, han sido diseñados para complementar módulos en [Microsoft Learn](https://learn.microsoft.com/training/paths/get-started-azure-ai/) en los que encontrarás un análisis más profundo de algunos de los conceptos subyacentes en los que se basan estos ejercicios.
