🎯 Proyecto
SaaS de venta de entradas con control de acceso por QR para eventos (discotecas, fiestas). El diferencial: detección de entradas duplicadas con validación por recibo, toggle entrada/salida y dashboard de afluencia en vivo.

Restricción económica: coste 0 € durante todo el desarrollo y pruebas (solo planes free). Único coste previsto: dominio (~10 €/año) en lanzamiento.

🛠️ Stack decidido
Frontend: Next.js + Tailwind (despliegue en Vercel, free)
Backend/BD: Supabase (PostgreSQL + Auth, plan free)
App lectora: PWA con cámara (sin tiendas de apps)
Pagos: Stripe Checkout en modo test
Email: Resend (free 100/día)
Gráficas: Recharts
Diseño: Figma (free)
Código: GitHub
🗓️ Plan de fases (~22 semanas a tiempo parcial)
Fase	Contenido	Duración
0 ✅	Definición MVP, wireframes, decisiones	2 sem
1 ⏳	Backend: BD + auth + API + QR firmado	3 sem
2	Web organizador + compra con Stripe test	4 sem
3	PWA lectora con cámara	3 sem
4	Dashboard métricas	3 sem
5	Prueba real controlada (fiesta con amigos)	2 sem
6	Piloto con evento real + dominio	5 sem
7	Monetización y escala	continuo
✅ Roles del MVP
Superadmin: gestiona clientes
Organizador: web (crear eventos, ventas, métricas)
Portero: app lectora (escanear QR, semáforo ✅/⚠️/❌)
Cliente final: compra entrada, recibe QR por email
❌ Fuera del MVP (aparcado)
White label, reembolsos automáticos, app nativa en tiendas, SMS/WhatsApp, suscripciones automáticas del SaaS, eventos multidía complejos.

🗄️ Estado técnico actual (Fase 0 completada, BD diseñada)
Esquema creado (SQL listo para ejecutar en Supabase):
organizations — organizadores/clientes del SaaS (plan: trial/basic/pro)
profiles — usuarios ligados a Supabase Auth, con organization_id
staff_members — personal de puerta con access_code (sin email)
events — con capacity, status (draft/published/closed/finished)
ticket_types — precio en céntimos (price_cents), máximo 2 tipos en MVP
tickets — campos clave: qr_code (unique), access_status (outside/inside/blocked/revoked), inside_count, payment_status, payment_ref
access_logs — cada escaneo: action (entry/exit/duplicate_denied/duplicate_allowed), scanned_by, device_id, timestamp
scanning_devices — con device_key para auth de la app lectora
Funciones RPC creadas:
process_scan(p_qr_code, p_event_id, p_staff_id, p_device_id) — función atómica con SELECT ... FOR UPDATE (row lock) que evita duplicados simultáneos. Lógica: ticket no existe → red; no pagado → rojo; bloqueado → rojo; outside → entrada (verde); inside → salida (toggle).
force_duplicate_entry(...) — portero valida duplicado con recibo en mano, registra como duplicate_allowed.
RLS:
Organizadores solo ven sus eventos (via profiles.organization_id). La app lectora se autentica con device_key y SOLO puede llamar a las RPC, nunca leer tablas directamente.

Firma del QR:
payload = qr_code | event_id | expiración + HMAC-SHA256 con secreto del servidor (QR_SIGNING_SECRET en env vars). El escáner verifica la firma antes de llamar a la BD.

⚠️ Pendiente de decidir/ajustar (próximo paso técnico):
Añadir campo mode a scanning_devices (entry | exit | toggle) y parámetro p_mode a process_scan:

mode=entry + ticket inside → duplicado (ámbar, no cambia estado)
mode=exit + ticket inside → salida directa
mode=toggle → comportamiento actual
📌 Siguiente paso
FASE 1 — Backend:

Ejecutar SQL en Supabase (tablas → funciones → RLS)
Probar process_scan con 3 casos (entrada, toggle, QR falso)
Insertar datos de prueba
Auth de organizadores
Endpoints de eventos
Generación de tickets con QR firmado
Envío de email con ticket
Después de eso: Fase 2 (páginas web del panel y compra).
