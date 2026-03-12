<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PERFUMES L&R - LUIS BONILLA / RAUL TORRES</title>

<style>
    /* ============================================================
       DISEÑO Y ESTILOS (CSS)
       Aquí controlas la apariencia de la app.
       ============================================================ */
    body{ font-family: Arial, sans-serif; background:#111; color:white; padding:15px; line-height: 1.4; }
    h2{ text-align:center; color: #00e676; }
    h3{ border-bottom: 1px solid #333; padding-bottom: 5px; margin-top: 20px; font-size: 16px; color: #aaa; }
    
    /* Estilo de los cuadros de texto */
    input{ width:100%; padding:10px; margin:5px 0; border-radius:8px; border:none; font-size:16px; box-sizing: border-box; }
    
    /* Estilo especial para el cuadro de Perfumol (Gris porque es automático) */
    input:read-only { background: #333; color: #00ff00; font-weight: bold; }
    
    /* Botón de calcular con efecto al presionar */
    button{ width:100%; padding:15px; margin-top:20px; border:none; border-radius:10px; font-size:18px; background:#00c853; color:white; font-weight:bold; cursor: pointer; }
    button:active { background: #009624; }

    /* Contenedor de los resultados finales */
    .resultado{ background:#222; padding:15px; border-radius:10px; margin-top:15px; border: 1px solid #444; }
    .alerta{ color:#ff5252; font-weight:bold; text-align: center; margin: 10px 0; }
    .ok{ color:#00e676; font-weight:bold; text-align: center; margin: 10px 0; }
    
    /* Diseño de las filas de precios (Moneda a la izquierda, Monto a la derecha) */
    .seccion-precio { margin-bottom: 15px; }
    .titulo-precio { font-weight: bold; color: #00e676; text-transform: uppercase; font-size: 14px; margin-bottom: 5px; }
    .fila { display: flex; justify-content: space-between; border-bottom: 1px solid #333; padding: 4px 0; }
    .moneda { color: #aaa; }
    .monto { font-family: monospace; font-size: 15px; }
    hr { border: 0; border-top: 2px dashed #444; margin: 15px 0; }
</style>
</head>

<body>

<h2>PERFUMES L&R</h2>
<p style="text-align:center;">
Nota, de vez en cuando pesar los insumos: <br> ESENCIA 1LT = 950GR <br> FIJADOR 1LT = 1020GR <br> FEROMONA 1LT = 1020GR <br> PERFUMOL 1LT = 776GR</p>

<h3>FÓRMULA (CANTIDADES)</h3>
Capacidad de Minilitros del Envase 
<input type="number" id="ml_envase" placeholder="Capacidad (7ML 30ML 50ML 60ML 100ML 120ML ...)">
Gramos de Esencia a Usar
<input type="number" id="esencia_gr" placeholder="Esencia POR 1ML/0.33GR (30ML/10GR 50ML/16.66GR ...)">
Gramos de Fojador a Usar
<input type="number" id="fijador_gr" placeholder="Fijador POR 1ML/0.03GR (30ML/1GR 50ML/1.66GR ...)">
Gramos de Feromona  a Usar 
<input type="number" id="feromonas_gr" placeholder="Feromona POR 1ML/0.03GR (30ML/1GR 50ML/1.66GR ...)">

<label style="font-size: 11px; color: #00e676; margin-left: 5px;">Perfumol Automático (gr):</label>
<input type="number" id="perfumol_gr" placeholder="Perfumol" readonly>

<h3>PRECIOS INSUMOS (USD)</h3>
Precio por Gramo de Esencia "$ 0.056"
<input type="number" id="precio_esencia" placeholder="$ 0.056 Costo 1Gr Esencia">
Precio por Gramo de fijador "$ 0.010"
<input type="number" id="precio_fijador" placeholder="$ 0.010 Costo 1Gr Fijador">
Precio por Gramo de feromona "$ 0.010"
<input type="number" id="precio_feromona" placeholder="0.010 Costo 1Gr Feromona">
Precio por Gramo de Perfumol "$ 0.004"
<input type="number" id="precio_perfumol" placeholder="$ 0.004 Costo 1Gr Perfumol">
Precio del Envase
<input type="number" id="precio_envase" placeholder="$ indique el Costo del Envase">
Gastos extras "$ 0.02"
<input type="number" id="extras_usd" placeholder="$ Costos (Etiquetas, etc)">

<h3>TASAS Y GANANCIA</h3>
Tasa P2P
<input type="number" id="tasa_p2p" placeholder="Bs Tasa P2P">
Tasa BCV
<input type="number" id="tasa_bcv" placeholder="Bs Tasa BCV">
% de Ganacia
<input type="number" id="ganancia" placeholder="Minimo 50%">

<button onclick="calcular()">CALCULAR PRECIOS</button>

<div class="resultado" id="resultado">
    <p style="text-align:center; color:#666;">Ingresa los datos y presiona calcular</p>
</div>

<script>
/**
 * FUNCIÓN CALCULAR
 * Se ejecuta al hacer clic en el botón.
 */
function calcular(){
    
    // 1. CAPTURA DE VARIABLES (INPUTS)
    // Buscamos los valores por su ID y los convertimos a números decimales (parseFloat).
    // Si el campo está vacío, usamos 0 ( || 0 ).
    let ml_envase = parseFloat(document.getElementById("ml_envase").value) || 0;
    let esencia_gr = parseFloat(document.getElementById("esencia_gr").value) || 0;
    let fijador_gr = parseFloat(document.getElementById("fijador_gr").value) || 0;
    let feromonas_gr = parseFloat(document.getElementById("feromonas_gr").value) || 0;    
    let p_esencia = parseFloat(document.getElementById("precio_esencia").value) || 0;
    let p_fijador = parseFloat(document.getElementById("precio_fijador").value) || 0;
    let p_fero = parseFloat(document.getElementById("precio_feromona").value) || 0;
    let p_perf = parseFloat(document.getElementById("precio_perfumol").value) || 0;
    let p_envase = parseFloat(document.getElementById("precio_envase").value) || 0;
    let extras = parseFloat(document.getElementById("extras_usd").value) || 0;
    
    let t_p2p = parseFloat(document.getElementById("tasa_p2p").value) || 0;
    let t_bcv = parseFloat(document.getElementById("tasa_bcv").value) || 0;
    let porc_ganancia = (parseFloat(document.getElementById("ganancia").value) || 0) / 100;

    // 2. LÓGICA DE RELLENO AUTOMÁTICO (PERFUMOL)
    // Convertimos los gramos de los activos a ML usando su densidad.
    // Densidades: Esencia (0.95), Fijador (1.02), Feromonas (1.02).
    let ml_activos = (esencia_gr / 0.95) + (fijador_gr / 1.02) + (feromonas_gr / 1.02);
    
    // Restamos la capacidad total menos lo que ya ocupan los activos.
    // Math.max(0, ...) asegura que si te pasas, el resultado sea 0 y no un número negativo.
    let ml_perfumol = Math.max(0, ml_envase - ml_activos);
    
    // Convertimos los ML de perfumol sobrantes a GRAMOS (Densidad alcohol: 0.776).
    let perfumol_gr = ml_perfumol * 0.776;
    
    // Mostramos el resultado del Perfumol en el campo de lectura.
    document.getElementById("perfumol_gr").value = perfumol_gr.toFixed(2);

    // 3. CÁLCULO DE COSTO BASE (USD)
    // Fórmula: (Masa * Precio) de cada componente + Envase + Gastos Extras.
    let costo_base_usd = (esencia_gr * p_esencia) + 
                         (fijador_gr * p_fijador) + 
                         (feromonas_gr * p_fero) + 
                         (perfumol_gr * p_perf) + 
                         p_envase + extras;

    // 4. CÁLCULO DE PRECIO DE VENTA (USD)
    // Fórmula: Costo base aumentado por el porcentaje de ganancia.
    let precio_venta_usd = costo_base_usd * (1 + porc_ganancia);

    // 5. SISTEMA DE ALERTAS (VALIDACIÓN)
    // Comprobamos si el volumen de los ingredientes cabe en el frasco.
    let status = (ml_activos > ml_envase) ? 
        "<div class='alerta'>⚠ EL CONTENIDO EXCEDE EL ENVASE</div>" : 
        "<div class='ok'>✔ FÓRMULA EQUILIBRADA</div>";

    // 6. GENERACIÓN DE LA SALIDA (HTML FINAL)
    // Inyectamos todo el texto y cálculos finales en el div de resultados.
    document.getElementById("resultado").innerHTML = `
        ${status}
        <div style="text-align:center; font-size:12px; color:#aaa; margin-bottom:15px;">
            Peso Total: ${(esencia_gr + fijador_gr + feromonas_gr + perfumol_gr).toFixed(2)} gr | 
            Volumen Final: ${(ml_activos + ml_perfumol).toFixed(2)} ml
        </div>

        <div class="seccion-precio">
            <div class="titulo-precio">📉 COSTO DE PRODUCCIÓN (Inversión)</div>
            <div class="fila"><span class="moneda">P2P Tasa: Bs ${t_p2p}</span><span class="monto">$${costo_base_usd.toFixed(2)} | ${(costo_base_usd * t_p2p).toFixed(2)} Bs</span></div>
            <div class="fila"><span class="moneda">BCV Tasa: Bs ${t_bcv}</span><span class="monto">$${((costo_base_usd * t_p2p) / t_bcv || 0).toFixed(2)} | ${(((costo_base_usd * t_p2p) / t_bcv) * t_bcv || 0).toFixed(2)} Bs</span></div>
        </div>

        <hr>

        <div class="seccion-precio">
            <div class="titulo-precio">💰 PRECIO DE VENTA (Ganancia ${porc_ganancia*100}%)</div>
            <div class="fila"><span class="moneda">P2P Tasa: Bs ${t_p2p}</span><span class="monto"><b>$${precio_venta_usd.toFixed(2)} | ${(precio_venta_usd * t_p2p).toFixed(2)} Bs</b></span></div>
            <div class="fila"><span class="moneda">BCV Tasa: Bs ${t_bcv}</span><span class="monto"><b>$${((precio_venta_usd * t_p2p) / t_bcv || 0).toFixed(2)} | ${(((precio_venta_usd * t_p2p) / t_bcv) * t_bcv || 0).toFixed(2)} Bs</span></div>
        </div>

         <hr>
         
         <div class="seccion-precio">
            <div class="titulo-precio">GANANCIA (tras la venta)</div>
             <div class="fila"><span class="moneda">P2P Tasa: Bs ${t_p2p}</span><span class="monto"><b>$${(precio_venta_usd-costo_base_usd).toFixed(2)} | ${((precio_venta_usd-costo_base_usd) * t_p2p || 0).toFixed(2)} Bs</span></div>
            </div>

            <div style="text-align:center; font-size:12px; color:#aaa; margin-bottom:15px;">
            <br> NOTA: <br> la ganacia tras la venta se recibe en "bs" <br>
            Es decir que esta propenso a desvaluarse por la volatilidad <br>
            al reinvertir los Bs a p2p: <br>
            la tasa de compra no de ser mayor ${t_p2p} Bs | 
            
        </div>


    `;
}
</script>
</body>
</html>
