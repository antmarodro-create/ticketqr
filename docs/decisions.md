# Decisiones de diseño — TicketQR

## D1: Modo del escáner (PENDIENTE ⚠️)
- Opciones: `toggle` (una puerta alterna), `entry` (solo entrada), `exit` (solo salida)
- Plan: añadir campo `mode` a `scanning_devices` y parámetro `p_mode` a `process_scan`
- mode=entry + ticket inside → duplicado (ámbar, sin cambiar estado)
- mode=exit + ticket inside → salida directa
- **Decisión final**: por confirmar antes de escribir código de app

## D2: Stack
Next.js + Tailwind, Supabase, PWA lectora, Stripe test, Resend, Recharts.
Despliegue: Vercel. Desarrollo: GitHub Codespaces desde móvil.

## D3: Restricción económica
Coste 0 € en desarrollo (planes free). Solo coste previsto: dominio (~10 €/año) al lanzar.

