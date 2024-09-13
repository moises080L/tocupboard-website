# Proyecto ToCupboard

Este repositorio contiene el código y la documentación del proyecto de desarrollo de la página web de ToCupboard, enfocado en la integración de prácticas de DevSecOps.

## Contenido

- **docs/**: Documentación sobre el modelo de amenazas, guías de codificación y pruebas de seguridad.
- **src/**: Código fuente del proyecto.
  - **js/**: Scripts JavaScript del proyecto.
  - **css/**: Estilos CSS del proyecto.
  - **php/**: Código PHP para manejar las APIs.

## Objetivos del Proyecto

- Implementar un Secure SDLC.
- Desarrollar un modelo de amenazas.
- Establecer guías de codificación segura.
- Realizar pruebas de seguridad automatizadas.
- Implementar un sistema de monitoreo y auditoría.

### Contenido de Archivos de Documentación

#### `docs/modelo_amenazas.md`

```markdown
# Modelo de Amenazas

Este documento describe el modelo de amenazas para el proyecto ToCupboard.

## Identificación de Activos

- Datos de usuario
- Información de productos
- Código fuente del sitio web

## Amenazas Identificadas

1. **Acceso no autorizado**: Posibilidad de que un atacante obtenga acceso a áreas restringidas.
2. **Inyección de código**: Riesgo de que se inserte código malicioso en el sistema.
3. **Denegación de servicio**: Ataques que buscan hacer el sitio web inaccesible.

## Mitigaciones

- Implementar autenticación robusta.
- Validar y sanitizar todas las entradas de usuario.
- Monitorear el tráfico del servidor para detectar actividad inusual.
# Guías de Codificación Segura

Este documento establece las mejores prácticas de codificación para el proyecto.

## Mejores Prácticas

1. **Validación de entradas**: Siempre validar y sanitizar entradas del usuario.
2. **Uso de HTTPS**: Asegurarse de que todas las comunicaciones se realicen sobre HTTPS.
3. **Manejo de errores**: No revelar información sensible en mensajes de error.

## Recursos Adicionales

- OWASP Secure Coding Practices
- Documentación de PHP sobre seguridad
# Pruebas de Seguridad

Este documento detalla el enfoque de pruebas de seguridad para el proyecto.

## Tipos de Pruebas

1. **Pruebas de SAST (Static Application Security Testing)**: Análisis del código fuente en busca de vulnerabilidades.
2. **Pruebas de IAST (Interactive Application Security Testing)**: Análisis en tiempo de ejecución.
3. **Pruebas de penetración**: Simulación de ataques para identificar vulnerabilidades.

## Herramientas Utilizadas

- OWASP ZAP
- SonarQube
- Burp Suite
#src/php/tocupboard-api.php
<?php
/*
Plugin Name: ToCupboard API
Description: Plugin para manejar llamadas a la API.
Version: 1.0
Author: Tu Nombre
*/

// Hook para inicializar el plugin
add_action('init', 'tocupboard_api_init');

function tocupboard_api_init() {
    add_action('wp_ajax_get_products', 'tocupboard_get_products');
    add_action('wp_ajax_nopriv_get_products', 'tocupboard_get_products');

    add_action('wp_ajax_send_contact_form', 'tocupboard_send_contact_form');
    add_action('wp_ajax_nopriv_send_contact_form', 'tocupboard_send_contact_form');
}

function tocupboard_get_products() {
    $response = wp_remote_get('https://api.example.com/products');

    if (is_wp_error($response)) {
        wp_send_json_error('Error al obtener productos');
        return;
    }

    $products = json_decode(wp_remote_retrieve_body($response), true);
    wp_send_json_success($products);
}

function tocupboard_send_contact_form() {
    $name = sanitize_text_field($_POST['name']);
    $email = sanitize_email($_POST['email']);
    $message = sanitize_textarea_field($_POST['message']);

    $response = wp_remote_post('https://api.example.com/contact', [
        'body' => json_encode([
            'name' => $name,
            'email' => $email,
            'message' => $message,
        ]),
        'headers' => [
            'Content-Type' => 'application/json',
        ],
    ]);

    if (is_wp_error($response)) {
        wp_send_json_error('Error al enviar el formulario');
        return;
    }

    wp_send_json_success('Formulario enviado correctamente');
}
