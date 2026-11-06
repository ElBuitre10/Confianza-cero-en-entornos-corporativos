# 🛡️ Hardening Perimetral y Segregación OT/IT en Infraestructuras Críticas Frigoríficas

## 📝 Caso de Estudio y Descripción del Proyecto
Este repositorio contiene el diseño, despliegue y validación analítica de una arquitectura de red perimetral segura basada en los principios de **Confianza Cero (Zero Trust)**. El laboratorio toma como referencia el ciberataque masivo real perpetrado contra sistemas de refrigeración industrial en Europa, donde fallas críticas de segmentación y la exposición pública de protocolos de administración web (HTTP/HTTPS) en controladores Danfoss/Copeland permitieron el sabotaje remoto de las temperaturas, quebrando la cadena de frío de múltiples sucursales.

La solución rompe con los esquemas de infraestructura planos tradicionales e implementa un modelo de **Defensa en Profundidad (Defense in Depth)**. Mediante redes lógicas virtuales (VLANs), se aíslan por completo los activos críticos de producción y telemetría de los entornos comerciales y perfiles externos hostiles, controlando el tráfico mediante un firewall de inspección de estado por puerto en el núcleo de la red.

---

## 🗺️ Arquitectura de la Red Industrial
La topología se diseñó de forma jerárquica en **Cisco Packet Tracer** estructurando los siguientes componentes y segmentos lógicos:

*   **Core Perimetral / Firewall (Layer 3):** Administrado bajo el host **El3UITRE** (Router Cisco ISR4331), encargado de gobernar el enrutamiento inter-VLAN bajo el estándar `802.1Q` (Router-on-a-Stick).
*   **Conmutación Corporativa (Layer 2):** Desplegado sobre un Switch Cisco Catalyst 2960 (**Switch0**), encargado de la contención física de los puertos de acceso.
*   **Segmentación del Ecosistema:**
    *   **VLAN 10 - Red Comercial e IT:** 
        *   `PC HACKER`: Origen del vector de amenaza y escaneo perimetral (`IP: 192.168.10.2`).
        *   `Operador_Supermercado`: Estación legítima de gestión administrativa (`IP: 192.168.10.3`).
    *   **VLAN 20 - Red Industrial OT (Clúster de Frío):**
        *   `Nevera Carnicos`: Controlador industrial frigorífico crítico (`IP: 10.0.20.2`).
        *   `Nevera Lacteos`: Controlador industrial frigorífico para lácteos (`IP: 10.0.20.3`).
        *   `Servidor de Sensores de Neveras`: Nodo centralizado de telemetría y monitoreo térmico (`IP: 10.0.20.4`).

---

## 📊 Matriz de Control de Flujo de Datos (Políticas Zero Trust)

| Segmento Origen | Segmento Destino | Puerto / Protocolo | Estado | Acción y Mitigación de Seguridad |
| :--- | :--- | :--- | :--- | :--- |
| **PC HACKER (VLAN 10)** | Red Industrial (VLAN 20) | TCP / 80 (HTTP) | **DENIED** | Bloqueo perimetral automático ante intentos de inyección web. |
| **PC HACKER (VLAN 10)** | Red Industrial (VLAN 20) | TCP / 443 (HTTPS) | **DENIED** | Intercepción de accesos no autorizados a consolas SCADA. |
| **OPERADOR (VLAN 10)** | Red Industrial (VLAN 20) | TCP / Any | **DENIED** | Mitigación estricta de Movimiento Lateral; aislamiento interno completo. |
| **OPERADOR (VLAN 10)** | Core Gateway (El3UITRE) | IP / ICMP | **ALLOWED** | Tráfico administrativo y conectividad WAN legítima fluyendo con normalidad. |

---

## 🔍 Análisis Forense y Validación del Tráfico
El ecosistema fue sometido a auditorías analíticas en el buffer de eventos de simulación. Se validaron de forma científica dos comportamientos clave:

1.  **Aislamiento en Capa 2 (Inundación Unicast Desconocida):** Tras reubicar las interfaces a sus VLANs correspondientes, el Switch0 contuvo con éxito las tramas de difusión, impidiendo la fuga de información (*Sniffing*) hacia la interfaz del hacker.
2.  **Mitigación de Movimiento Lateral (Capa 3/4):** Al disparar tráfico desde el operador hacia la red de neveras, las subinterfaces lógicas de **El3UITRE** interceptaron las cabeceras en la aduana de red, ejecutando un descarte de paquetes estricto (**Packet Drop**) y bloqueando tanto vectores externos como amenazas internas comprometidas.

---

## 🖼️ Evidencia Gráfica del Laboratorio

### 1. Topología Física Jerárquica y Peinado de Cableado
`[Adjuntar aquí la captura de tu esquema completo en verde brillante]`

### 2. Tabla de VLANs Asignadas Activas en el Switch0
`[Adjuntar aquí la captura de la consola del Switch0 mostrando los puertos Fa0/2-5 en las VLANs 10 y 20]`

### 3. Intercepción Perimetral y Packet Drop en Acción (X Roja)
`[Adjuntar aquí tu última captura del sobre con la X roja demostrando el descarte del tráfico]`
