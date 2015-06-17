---
author: chubu
comments: true
date: 2007-04-21 17:45:39+00:00
layout: post
published: false
slug: No Content Found
title: AJAX - Introducción
wordpress_id: 34
categories:
- Editorial
---

Muchos ya estarán desarrollando sus aplicaciones web utilizando las diferentes técnicas que se esconden tras el acrónimo AJAX ("Asynchronous JavaScript and XML"), pero otros tantos starán preguntandose de que se trata el tema o tratano de comprender como funciona alguna de las tantas librerías que existen. En este primer artículo, a modo de introducción, trataremos de cubrir los detalles técnicos sobre los que se basa este grupo de técnicas.<!-- more -->

AJAX, básicamente, es un grupo de técnicas que nos van a permitir acelerar el intercambio de informacion entre el cliente (el navegador web) y el servidor web. La idea fundamental es achicar la cantidad de datos que van y vienen desde y hacia el servidor para lograr así tiempos de respuesta más breves y una interacción más fluida entre el usuario y la aplicación.

Las aplicaciones de estas técnicas son infinitas, desde el enriquecimiento de aplicaciones web clásicas, hasta el desarrollo en capas aisladas, separando completamente el cliente del servidor.

Para comenzar a meternos en tema vamos a ver la piedra fundamental de este grupo de técnicas:

**El objeto XMLHttpRequest**

Hoy por hoy disponible en las implmentaciones de JavaScript de la mayoría de los navegadores web, el objeto XMLHttpRequest nos permite realizar peticiones a un servidor web desde código JavaScript. Esto nos permite que una página que ya fue enviada al navegador web pueda interactuar con el servidor en base al accionar del usuario sin necesidad de generar una recarga completa de la página.
Debido a diferencias entre las diversas implementaciones del objeto (principalmnte entre Internet Explorer y Mozilla) debemos tomar ciertos recaudos para crear una instancia del objeto, como podemos ver a continuación:

[js]var xmlhttp = false;

try {
xmlhttp = new ActiveXObject("Msxml2.XMLHTTP");
} catch (e) {
try {
xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
} catch (E) {
xmlhttp = false;
}
}

if (!xmlhttp && typeof XMLHttpRequest != 'undefined') {
try {
xmlhttp = new XMLHttpRequest();
} catch (e) {
xmlhttp=false;
}
}

if (!xmlhttp && window.createRequest) {
try {
xmlhttp = window.createRequest();
} catch (e) {
xmlhttp=false;
}
}[/js] 
