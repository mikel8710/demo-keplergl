---
theme: [parchment, wide]
title: Proceso Crediticio - Gestion ANS
toc: false
---
<h2 style="font-family:sans-serif">Estatus de Propuestas</h2>
<span class="muted" style="font-family:sans-serif">Vistas resumen de volumetría y ANS del proceso crediticio de BM, actualizado a</span>


```js
import {Trend} from "./components/trend.js";
import {DailyPlot} from "./components/dayliattends.js";
import {charLegend} from "./components/legends.js";
import {chartSwatches} from "./components/legends.js";

```

```js
//Data Sources

//Fake

const data_grid = FileAttachment("data/stock_pptas.csv").csv({typed: true});


const tobe_ans_stages = [
{statusTime: '1.En revisión - EyAC', target:9.1 },
{statusTime: '2.En Consultas - EyAC', target:17.2 },
{statusTime: '5.Devuelta', target:15 },
{statusTime: '3.Elaboración de informe - EyAC', target:6.6 },
{statusTime: '4.Aprobacion', target:3.9 },
];

const f = d3.format("d");
const unDec = d3.format(".1f");

const statusCreditos = ['1.En revisión - EyAC','3.Elaboración de informe - EyAC','4.Aprobacion']
const statusComercial = ['2.En Consultas - EyAC','5.Devuelta']

const dateFormat = d3.utcFormat("%b-%y"); //Sep-24
const dateFormat_YYYYMM = d3.utcFormat("%Y%m"); //202409

```



<style>
@import url('https://fonts.googleapis.com/css?family=Roboto+Condensed&display=swap');
    /* Parent container */
    .container_div {
      display: flex; /* Align children in a row */
     justify-content: center; /* Even spacing */
      width: 100%;
      margin: auto; /* Center the container */
      padding: 10px;
    }
        /* Child divs */
    .box_div {
     width: 30%; /* Each box takes 30% of parent */
    }
     h2.with-brand {
      display: flex;
      align-items: center;
    }
    .with-brand .rectbrand {
      display: inline-block;
      width: 6px;             /* thickness */
      height: 35px;           /* desired height */
      margin-right: 10px;     /* space between bar and text */
      background: linear-gradient(
        to bottom,
        #16326a 50%,           /* top half = #284680 */
        #FFDC09 50%            /* bottom half = #FFDC09 */
      );
    }
    @container (min-width: 560px) {
    .grid-cols-2-3 {
      grid-template-columns: 1fr 1fr;
    }
    .grid-cols-2-3 .grid-colspan-2 {
      grid-column: span 2;
    }
    @container (min-width: 840px) {
    .grid-cols-2-3 {
      grid-template-columns: 1fr 2fr;
      grid-auto-flow: column;
      }
    }
    @container (min-width: 560px) {
    .grid-cols-5 {
      grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
      }
    .grid-cols-5 .grid-colspan-5 {
      grid-column: span 5;
      }
    }
</style>

```js

const mx_date_gbl_slide_view = d3.max(data_grid, (d) => (d.mes_stock));
const mn_date_gbl_slide_view = d3.min(data_grid, (d) => (d.mes_stock));
```

```js
const lst_leaders = Array.from(new Set(data_grid.map(d => d.Lider)))
const lst_officials = Array.from(new Set(data_grid.map(d => d.Funcionario)))

const sel_funcionario = Inputs.select(["Todos"].concat(lst_officials),{width:7});
const sel_funcionario_view = Generators.input(sel_funcionario);


const sel_leader= Inputs.select(["Todos"].concat(lst_leaders),{width:7});
const sel_leader_view = Generators.input(sel_leader);


const rmv_rfsd = Inputs.checkbox(["Quitar"], {value: "Quitar"});
const rmv_rfsd_view = Generators.input(rmv_rfsd);

const lvlAprob = Inputs.select(["Todos"].concat(["VPC+VPB","CI","CII", "CIII"]),{value: "Todos", width:7});
const lvlAprob_view = Generators.input(lvlAprob);

const viewLeader = Inputs.radio((["Leader","Funcionario"]),{value: "Leader", width:7});
const viewLeader_view = Generators.input(viewLeader);

const search_inpt = Inputs.search(data_grid, {placeholder: "Search in stock…"});
const search_view = Generators.input(search_inpt);

const metric_type = Inputs.select((["QTY","Time"]),{value: "QTY", width:7});
const metric_type_view = Generators.input(metric_type);

const option_metric_type = Inputs.radio(["QTY", "Time"], {value: "QTY"});
const option_metric_type_view = Generators.input(option_metric_type);

const typeOpe = Inputs.select(["Todos"].concat(["Renovación","MP","Puntual", "Otros","Nuevo"]),{value: "Todos", width:7});
const typeOpe_view = Generators.input(typeOpe);

//slide para las tarjetas de principales indicadores
const qmonths_slide_kpis =  d3.timeMonth.count(mn_date_gbl_slide_view, mx_date_gbl_slide_view);
const nthMonth_slide_kpis = Inputs.range([qmonths_slide_kpis,0], { step: 1,width: 50});
const qmes_gbl_slide_kpis = Generators.input(nthMonth_slide_kpis);
nthMonth_slide_kpis.querySelector("input[type=number]").remove();

```

```js
const bar_time_date_chng_kpis =  d3.utcMonth.offset(mx_date_gbl_slide_view, -qmes_gbl_slide_kpis);
//display(bar_time_date_chng_kpis)
```
<div class="grid grid-cols-1">
  <div class="card">
    <h2 class="with-brand" style="font-family:Source Sans 3, sans-serif;font-weight:510;font-style:light;color:#1C0421"><span class="rectbrand"></span>
    Principales Indicadores de Gestión de Propuestas:<div style="display: flex; align-items: center; float:left;padding-left: 0.5rem;"><h6>${dateFormat(mn_date_gbl_slide_view)}</h6> <h6>${nthMonth_slide_kpis}</h6> <h6 style="padding-left: 0.5rem;">${dateFormat(mx_date_gbl_slide_view)}</h6></div></h2>
  </div>
</div>


<div class="grid grid-cols-5">
  <div class="card">
    <h2>Propuestas<span class="small muted"> / </span></h2>
    <span class="big">${q_stock}</span>
    <span></span>
    <span class="muted">MoM</span>
     <table>
      <tr>
        <td>Propuestas Nuevas:</td>
        <td align="right"></td>
      </tr>
      <tr>
      <td>Promedio U3M:</td>
        <td align="right"></td>
      </tr>
      <tr>
     </table>
  </div>
<div class="card">
    <h2>E2E<span class="small muted"> / </span></h2>
    <span class="big">${time_stock}</span>
    <span></span>
    <span class="muted">MoM</span>
     <table>
      <tr>
        <td>Créditos:</td>
        <td align="right"></td>
      </tr>
      <tr>
      <td>Comercial:</td>
        <td align="right"></td>
      </tr>
      <tr>
    </table>
  </div>
  <div class="card">
    <h2>E2E Créditos<span class="small muted"> / </span></h2>
    <span class="big">${time_creditos}</span>
    <span></span>
    <span class="muted">MoM</span>
     <table>
      <tr>
        <td>Propuestas Nuevas:</td>
        <td align="right"></td>
      </tr>
      <tr>
      <td>Promedio U3M:</td>
        <td align="right"></td>
      </tr>
      <tr>
    </table>
  </div>
  <div class="card">
    <h2>E2E Comecial<span class="small muted"> / </span></h2>
    <span class="big">${time_comercial}</span>
    <span></span>
    <span class="muted">MoM</span>
     <table>
      <tr>
        <td>Propuestas Nuevas:</td>
        <td align="right"></td>
      </tr>
      <tr>
      <td>Promedio U3M:</td>
        <td align="right"></td>
      </tr>
      <tr>
    </table>
  </div>
  <div class="card">
    <h2>E2E Sin Dev.<span class="small muted"> / </span></h2>
    <span class="big">${time_stock_sin_dev}</span>
    <span></span>
    <span class="muted">MoM</span>
     <table>
      <tr>
        <td>Propuestas Nuevas:</td>
        <td align="right"></td>
      </tr>
      <tr>
      <td>Promedio U3M:</td>
        <td align="right"></td>
      </tr>
      <tr>
    </table>
  </div>
</div>


<div class="grid grid-cols-1">
  <div class="card">
    <h2 class="with-brand" style="font-family:Source Sans 3, sans-serif;font-weight:510;font-style:light;color:#1C0421"><span class="rectbrand"></span>Estatus de Gestión de Propuestas:</h2>
    <span style="font-family:Source Sans 3, sans-serif;font-weight:500;font-style:light;">〇: En Análisis △: En Consultas  &#x25A2;: En Elab.Informe  &#9734; :  En Admisión  &#128326;: Devuelta</span>
    <span style="font: 10px sans-serif;">
  <p>Color según tiempo acumulado:
  <span style="font: 11px sans-serif;color:#006BA2;font-weight:700;"> ✎ </span>más de 5 días para ANS<span style="font: 11px sans-serif;color:#FFDC09;font-weight:700;"> ✎</span>< = 5 días para ANS<span style="font: 11px sans-serif;color:#a81414;font-weight:700;"> ✎ </span>días por encima de ANS</p>
  </span>
    <div class= "container_div"> 
        <div class ="box_div"><h6>View:</h6> ${viewLeader}</div>
        <div class ="box_div"><h6>Tipo Métrica:</h6> ${option_metric_type}</div>
        <div class ="box_div"><h6>Leader:</h6> ${sel_leader}</div>
        <div class ="box_div"><h6>Fucionario:</h6> ${sel_funcionario}</div>
        <div class ="box_div"><h6>Nivel Aprobador:</h6> ${lvlAprob}</div>
        <div class ="box_div"><h6>Tipo Operación:</h6> ${typeOpe}</div>
        <div class ="box_div"><h6>Devueltas:</h6> ${rmv_rfsd}</div>
    </div>
  </div>
</div>

```js
const data_grid_view_leader = data_grid_stock.map(d => {return {Lider : d.Lider,Funcionario: d.Funcionario,Status: d.Status,NoPpta: d.NoPpta,Cliente: d.Cliente,Threshold: d.Threshold,Days: d.Days,min_ing_glb: d.min_ing_glb,Analisis: d.Analisis,Consultas: d.Consultas,Elab_Informe: d.Elab_Informe,Aprobacion: d.Aprobacion,Devuelta: d.Devuelta, gpoView: d.Lider}});

const data_grid_real = data_grid_stock.map(d => {return {Lider : d.Lider,Funcionario: d.Funcionario,Status: d.Status,NoPpta: d.NoPpta,Cliente: d.Cliente,Threshold: d.Threshold,Days: d.Days,min_ing_glb: d.min_ing_glb,Analisis: d.Analisis,Consultas: d.Consultas,Elab_Informe: d.Elab_Informe,Aprobacion: d.Aprobacion,Devuelta: d.Devuelta, gpoView: d.Funcionario}});

```

```js
const data_grid_stock = data_grid.filter((d)=> dateFormat_YYYYMM(d.mes_stock) === dateFormat_YYYYMM(bar_time_date_chng_kpis) );

const d_status_creditos = data_grid_stock.filter((d) => d.Status ==="1.En revisión - EyAC" || d.Status === "3.Elaboración de informe - EyAC" || d.Status === "4.Aprobacion" );
const d_status_comercial = data_grid_stock.filter((d) => d.Status ==="2.En Consultas - EyAC" || d.Status === "5.Devuelta");

const time_stock_sin_dev = unDec(d3.mean(data_grid_stock.filter((d)=> d.Status != "5.Devuelta"), d => d.Days));

```

```js
const q_stock = data_grid_stock.length;
const time_stock = unDec(d3.mean(data_grid_stock, d => d.Days));
const time_creditos = unDec(d3.mean(d_status_creditos, d => d.Days));
const time_comercial = unDec(d3.mean(d_status_comercial, d => d.Days));

```


```js
const sel_data_raw = (opt_view) =>{
  const data_raw = opt_view === "Leader" ? data_grid_view_leader :  data_grid_real;
  return data_raw
}
```

```js
display(Inputs.table(data_table,{
  columns: [
    "Lider",
    "Funcionario",
    "Status",
    "NoPpta",
    "Cliente",
    "Days",
    "min_ing_glb",
    "Analisis",
    "Consultas",
    "Elab_Informe",
    "Aprobacion",
    "Devuelta"
  ],
  header: {
    Lider: "Lider",
    Funcionario: "Funcionario",
    Status: "Estatus",
    NoPpta: "NroPpta",
    Cliente: "Cliente",
    Days: "Días",
    min_ing_glb: "FechaIngreso",
    Analisis: "Analisis",
    Consultas: "Consultas",
    Elab_Informe: "ElabInforme",
    Aprobacion: "Aprobacion",
    Devuelta: "Devuelta",
  },format: {
    NoPpta: (x) => f(x)}
}))
```

```js
const sel_data_grid_rank = sel_data_grid(sel_leader_view, sel_funcionario_view, rmv_fltr,sel_data_raw(viewLeader_view) );

const selection_plot = view(grid_rank(sel_data_grid_rank,type_metric(option_metric_type_view),{width}));

```

```js
const sel_data_grid =(opt_leader, opt_offi, opt_chk, data)=>{
  const data_grid = opt_leader === "Todos" && opt_offi === "Todos" ? data 
      :opt_leader != "Todos" && opt_offi === "Todos" ? data.filter((d)=> d.Lider === opt_leader) 
      :opt_leader === "Todos" && opt_offi != "Todos" ? data.filter((d)=> d.Funcionario === opt_offi)
      :data.filter((d) => d.Lider === opt_leader && d.Funcionario === opt_offi);
  const data_grid_f = opt_chk === "non_remove" ? data_grid : data_grid.filter((d) => d.Status != "5.Devuelta");
  return data_grid_f;
}
```
```js
const rmv_devueltas = (option_filter) =>{
  const rmv_rfsd =  option_filter === "Quitar" ? "remove":"non_remove"  ;
  return rmv_rfsd;
};
```

```js
const type_metric = (option_filter) =>{
  const type_metric =  option_filter === "QTY" ? "count":"avgDays"  ;
  return type_metric;
};

//display(type_metric(metric_type_view))
```


```js
const sel_data_grid_detail =(selection_plot , data)=>{
  const data_grid = selection_plot.status != null ?  data.filter((d) => d.Status === selection_plot.status)
      :selection_plot.NoPpta != null ?   data.filter((d) => d.NoPpta  === selection_plot.NoPpta)
      : data;
  return data_grid;
}
```

```js
const data_table = selection_plot === null ?  sel_data_grid_rank : sel_data_grid_detail(selection_plot,sel_data_grid_rank);
```


```js
const rmv_fltr =  rmv_devueltas(rmv_rfsd_view[0]);
```


```js
//display(search_inpt)
//display(Inputs.table(search_view))
```

```js 
/* main chart ------------------------------------------------ */
function grid_rank(data, metric, {width}={}) {

  const byOfficial = d3.group(data, d => d.gpoView);
 
  byOfficial.forEach(rows => {
  rows
    .sort((a, b) => d3.ascending(new Date(a.min_ing_glb),
                                 new Date(b.min_ing_glb)))
    .forEach((d, i) => { d.seq = i + 1; });   // 1-based index per official
  });
 

  /* ---------- 1. derived sets & helpers ------------------- */
  //const officials = Array.from(new Set(data.map(d => d.Funcionario)));
  const statuses  = Array.from(new Set(data.map(d => d.Status))).sort();      // P columns

  //agregar esta parte para la mètrica debajo del figura de las cabeceras <<<------------
  const statusTotal = Object.fromEntries(
  //d3.rollup(data, v => v.length, d => d.Status)   // Map → object
  d3.rollup(data, metric === "count" ? v => v.length: v => unDec(d3.mean(v, d => d.Days)),d => d.Status));
 

  const proposalCount = d3.rollup(data, v => v.length, d => d.gpoView);
  const officials = Array.from(proposalCount.keys())
  .sort((a, b) => d3.descending(proposalCount.get(a), proposalCount.get(b)));

 /*
  const maxByOfficial = Object.fromEntries(
    d3.groups(data, d => d.Official)
       .map(([ofc, arr]) => [ofc, d3.max(arr, d => d.Order)])
  );
*/
  const maxByOfficial = Object.fromEntries( Array.from(byOfficial, ([ofc, arr]) => [ofc, arr.length]));

  //const globalMaxOrder = d3.max(data, d => d.Order);
  const globalMaxOrder = d3.max(data, d => d.seq);

  let clicked = null; 
  /* count proposals per (Official, Status) */
  const counts = d3.rollup(
    data,
    v => v.length,
    d => d.gpoView,
    d => d.Status
  ); // Map<Official, Map<Status, count>>

     /* average Days per (Official, Status) */
  const avgDaysByCell = d3.rollup(
     data,
     v => d3.mean(v, d => d.Days),
     d => d.gpoView,
     d => d.Status
  );

  /* avg Days per Status (across ALL officials) */
  const avgDays = Object.fromEntries(
    d3.groups(data, d => d.Status)
       .map(([st, arr]) => [st, d3.mean(arr, d => d.Days)])
  );
  
  /* target look-up */
  const targetMap = Object.fromEntries(
    tobe_ans_stages.map(d => [d.statusTime, d.target])
  );

  const headerColour = st => {
    const diff = (targetMap[st] ?? 0) - (avgDays[st] ?? 0);
    if (diff >= 5) return "#006BA2";           // green
    if (diff >= 0) return "#FFD700";           // yellow
    return "#a81414";                          // red
  };

  /* ---------- 2. layout & scales -------------------------- */
  const margin   = { top: 55, right: 30, bottom: 20, left: 190 };
  const rowH     = 31;
  const bandW    = 48;                         // width per status column
  const gap      = 45;                         // space between grids

  const height   = officials.length * rowH + margin.top + margin.bottom;

  const xStatus  = d3.scaleBand()
    .domain(statuses)
    .range([margin.left, margin.left + statuses.length * bandW])
    .paddingInner(0.15);

  const xOrder   = d3.scaleLinear()
    .domain([1, globalMaxOrder])
    .range([xStatus.range()[1] + gap, width - margin.right]);

  const yScale   = d3.scaleBand()
    .domain(officials)
    .range([margin.top, height - margin.bottom])
    .paddingInner(0.35);

  const sym = {
    "1.En revisión - EyAC":       d3.symbolCircle,
    "2.En Consultas - EyAC": d3.symbolTriangle,
    "3.Elaboración de informe - EyAC":        d3.symbolSquare,
    "4.Aprobacion":        d3.symbolStar,
    "5.Devuelta":        d3.symbolCross
  };

  /* ---------- 3. root SVG & tooltip ----------------------- */
   const div = d3.create("div")
    .style("overflow-x", "auto")
    .style("font-variant-numeric", "tabular-nums");

  const svg = div.append("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("style", "max-width: 100%; height: auto; font: 10px sans-serif;");

  const tooltip = d3.select("body").append("div")
    .style("position", "absolute")
    .style("pointer-events", "none")
    .style("visibility", "hidden")
    .style("background", "#fff")
    .style("border", "1px solid #ccc")
    .style("border-radius", "4px")
    .style("padding", "6px 8px")
    .style("font-size", "11px");
  
  const element = div.node();
  element.value = null;

  /* ---------- 4. grid lines ------------------------------- */
  /* status-grid verticals */
  statuses.forEach(st => {
    const x = xStatus(st);
    svg.append("line")
      .attr("x1", x).attr("x2", x)
      .attr("y1", margin.top).attr("y2", height - margin.bottom)
      .attr("stroke", "#000").attr("stroke-dasharray", "2,2")
      .attr("stroke-width", 1).attr("opacity", 0.35);
  });
  /* trailing boundary */
  svg.append("line")
     .attr("x1", xStatus.range()[1]).attr("x2", xStatus.range()[1])
     .attr("y1", margin.top).attr("y2", height - margin.bottom)
     .attr("stroke", "#000").attr("stroke-dasharray", "2,2")
     .attr("stroke-width", 1).attr("opacity", 0.35);

  /* order-grid verticals */
  for (let i = 1; i <= globalMaxOrder; ++i) {
    svg.append("line")
      .attr("x1", xOrder(i)).attr("x2", xOrder(i))
      .attr("y1", margin.top).attr("y2", height - margin.bottom)
      .attr("stroke", "#000").attr("stroke-dasharray", "2,2")
      .attr("stroke-width", 1).attr("opacity", 0.35);
  }

  /* horizontals + left label */
  officials.forEach(ofc => {
    const y = yScale(ofc);
    svg.append("line")
      .attr("x1", margin.left)
      .attr("x2", xOrder(globalMaxOrder))
      .attr("y1", y).attr("y2", y)
      .attr("stroke", "#000").attr("stroke-dasharray", "2,2")
      .attr("stroke-width", 1).attr("opacity", 0.35);

    svg.append("text")
      .attr("x", margin.left - 10).attr("y", y).attr("dy", "0.35em")
      .style("text-anchor", "end").style("font-size", 12).style("font-weight",500)
      .text(`${ofc} : ${maxByOfficial[ofc]}`);
  });

  /* ---------- 5. status-grid cell counts ----------------- */
  officials.forEach(ofc => {
    statuses.forEach(st => {
      //const cnt = counts.get(ofc)?.get(st) ?? 0;
      const val =
      metric === "count" ? counts.get(ofc)?.get(st) ?? 0 : avgDaysByCell.get(ofc)?.get(st) ?? 0;
      const txt = metric === "count" ? val : d3.format(".1f")(val);   // one decimal
 
      svg.append("text")
        .attr("x", xStatus(st) + xStatus.bandwidth() / 2)
        .attr("y", yScale(ofc)).attr("dy", "0.35em")
        .style("text-anchor", "middle").style("font-size", 13)
        //.text(cnt);
        .text(txt);
    });
  });

  //  a)  text above the order-grid
svg.append("text")
   .attr("x", xOrder(1))
   .attr("y", margin.top - 24)          // a bit above header shapes
   .style("font-size", 8.5)
   .text("de más antigua a más nueva →");
 
//  b)  vertical text left of official names
svg.append("text")
   .attr("transform", `translate(${margin.left-180}, ${(margin.top + height - margin.bottom) / 2}) rotate(-90)`)
   .style("text-anchor", "middle")
   .style("font-size", 8.5)
   .text("← de más a menos propuestas");

  /* ---------- 6. header shapes (colour by avg vs target) -- */
  statuses.forEach(st => {
    svg.append("path")
      .datum({ status: st })
      .attr("d", d3.symbol().type(sym[st]).size(240)())
      .attr("transform", `translate(${xStatus(st) + xStatus.bandwidth() / 2}, ${margin.top - 40})`)
      .attr("fill", headerColour(st))
      .attr("stroke", "black")
      .style("cursor", "pointer")      // visual cue
      .on("click", click)
      .on("dblclick",dblclick)
    
      /* ❷-count just under the shape  (NEW) agregar esta parte para la mètrica debajo del figura de las cabeceras <<<------------*/
  svg.append("text")
    .attr("x", xStatus(st) + xStatus.bandwidth() / 2)
    .attr("y", margin.top - 20)          // a few px below the icon
    .attr("dy", "0.35em")
    .style("text-anchor", "middle")
    .style("font-size", 11)
    .text((statusTotal[st]) ?? 0);        // e.g. 17
  });



  /* ---------- 7. order-grid proposal shapes -------------- */
  const colourByThreshold = d => {
    const diff = d.Threshold - d.Days;
    if (diff >= 7) return "#006BA2";
    if (diff >= 5) return "#FFD700";
    return "#a81414";
  };

  data.forEach(d => {
    const cx = xOrder(d.seq);
    const cy = yScale(d.gpoView);
    svg.append("path")
      .datum(d)
      .attr("d", d3.symbol().type(sym[d.Status]).size(200)())
      .attr("transform", `translate(${cx}, ${cy})`)
      .attr("fill", colourByThreshold(d))
      .attr("stroke", "black")
      .style("cursor", "pointer") 
      .on("mouseover", (event, d) => {
        tooltip.style("visibility", "visible")
          .html(`PptNo: ${d.NoPpta}<br/>Funcionario: ${d.Funcionario}<br/>Status: ${d.Status}<br/>Client: ${d.Cliente}<br/>E2E: ${d.Days}`);
      })
      .on("mousemove", event => {
        tooltip.style("top", `${event.pageY + 10}px`)
               .style("left", `${event.pageX + 10}px`);
      })
      .on("mouseout", () => tooltip.style("visibility", "hidden"))
      .on("click", click)
      .on("dblclick",dblclick)

  });

  function click(_, datum) {
    if (datum ) {

      clicked = datum;

      element.value = datum;
    } else {
      clicked = null;

      element.value = null;
    }
    element.dispatchEvent(new CustomEvent("input"));
  }
  function dblclick(_, datum) {
   
    element.value = null;

    element.dispatchEvent(new CustomEvent("input"));
  }
  /* ---------- 8. return ------------------------------- */
  //return svg.node();
  return element;
}
```
