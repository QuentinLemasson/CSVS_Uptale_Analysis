<template>
    <div id=VizContainer>
        <svg id=viz>

        </svg>
    </div>
</template>

<script>

import * as d3 from "d3";
import { proxyToStructure } from "../../utilities";

export default {
  name: "PathVisualization",
  props: {
    data: Array
  }, 
  watch: { 
    data: function(newData) { // watch it
        let data = proxyToStructure(newData).data;
        this.draw(data);
    }
  },
  mounted(){
        let data = proxyToStructure(this.data).data;
        this.draw(data);
  },
  methods : {
    draw(data){

        const global_container = d3.select('#viz')
        global_container.selectAll('*').remove()

        const width = global_container.node().getBoundingClientRect().width;
        const height = global_container.node().getBoundingClientRect().height;

        const margin = {top: 50, right: 150, bottom: 180, left: 50};
        const innerpadding = 10;

        const innerwidth=width - margin.left - margin.right;
        const innerheight=height - margin.top - margin.bottom;

        const axisSizes = data.map(e => e.zones.length);
        const axisTotalSize = axisSizes.reduce((a,b) => a+b);

        const innerwidths = axisSizes.map(e => (innerwidth-(innerpadding*(axisSizes.length-1))) * (e/axisTotalSize));

        // 6. Y scale will use the randomly generate number 
        var yScale = d3.scaleLinear()
            .domain([0, d3.max( data.map( d => d.access ) )]) // input 
            .range([innerheight, 0]); // output 

        // 4. Call the y axis in a group tag
        global_container.append("g")
            .attr("class", "y axis")
            .attr("transform", "translate("+ margin.left +"," + margin.top +")")
            .call(d3.axisLeft(yScale)
                .tickValues(yScale.ticks().filter(tick => Number.isInteger(tick)))
                .tickFormat(d3.format("d"))); // Create an axis component with d3.axisLeft

        data.forEach((d,i) => {
            const distanceLeft = innerpadding*i + (i==0?0:innerwidths.reduce((a,b,ind)=>ind<i?a+b:a)) + 5;
            let innerContainer = global_container.append("g").attr('class','SceneScoresInPath').attr("transform",`translate(${margin.left +distanceLeft},${margin.top})`)
            this.drawScene(innerContainer,d,yScale,innerwidths[i], innerheight, distanceLeft);
        });       
    },

    drawScene(container, data, yScale, innerwidth, innerheight, distanceLeft){

        container.append('text')
            .attr('x',10)
            .attr('y',-20)
            .text(data.id)
            .attr('font-size', 'smaller')


        // 5. X scale will use the index of our data
        let xScale = d3.scaleBand()
            .domain(data.zones.map(e=>e.tag)) // input
            .range([0,innerwidth])
            .paddingInner(0.05)
            .paddingOuter(0.2); // output

        container.append('rect')
            .attr("id", "innerContainer")
            .attr('x',0)
            .attr('y',0)
            .attr("width", innerwidth)
            .attr("height", innerheight)
            .attr("fill",'white');

        // 3. Call the x axis in a group tag
        const xAxis = d3.axisBottom(xScale);
        container.append("g")
            .attr("class", "x axis")
            .attr("transform", "translate(0," + innerheight +")")
            .call(xAxis) // Create an axis component with d3.axisBottom
            .selectAll("text")
                .attr("transform", "translate(80,70)rotate(45)");

        // 12. Appends a circle for each datapoint 
        let dots = container.append('g')
        // .attr('transform', `translate(${margin.left},${margin.top})`)

        dots.append('g').selectAll(".dot.founded")
            .data(data.zones)
            .join("rect") // Uses the enter().append() method
                .attr("class", "dot founded") // Assign a class for styling
                .attr("x", d => xScale(d.tag))
                .attr("y", d => yScale(d.founded))
                .attr("width", xScale.bandwidth())
                .attr("height", d =>  innerheight-yScale(d.founded))
                .on('mouseover',e => {
                    const d = e.originalTarget.__data__;
                    this.highlightDot(dots, xScale(d.tag)+xScale.bandwidth(),
                                            yScale(d.scored),
                                            yScale(d.founded),
                                            xScale(d.tag)+xScale.bandwidth()+distanceLeft,
                                            innerheight-yScale(d.scored),
                                            innerheight-yScale(d.founded))
                })
                .on('mouseout',() => this.destroyElements(dots,'.dashed_line'))

        dots.append('g').selectAll(".dot.scored")
            .data(data.zones)
            .join("rect") // Uses the enter().append() method
                .attr("class", "dot scored") // Assign a class for styling
                .attr("x", d => xScale(d.tag))
                .attr("y", d => yScale(d.scored))
                .attr("width", xScale.bandwidth())
                .attr("height", d =>  innerheight-yScale(d.scored))
                .on('mouseover',e => {
                    const d = e.originalTarget.__data__;
                    this.highlightDot(dots, xScale(d.tag)+xScale.bandwidth(),
                                            yScale(d.scored),
                                            yScale(d.founded),
                                            xScale(d.tag)+xScale.bandwidth()+distanceLeft,
                                            innerheight-yScale(d.scored),
                                            innerheight-yScale(d.founded))
                })
                .on('mouseout',() => this.destroyElements(dots,'.dashed_line'))

        this.drawLine(container, 'occurenceLine', {x:0,y:yScale(data.access)}, {x:innerwidth,y:yScale(data.access)})
    },

    highlightDot(container, x, ys, yf, w, hs, hf,){
        this.drawLine(container, 'dashed_line', {x:x-w,y:ys}, {x:x,y:ys})
        this.drawLine(container, 'dashed_line', {x:x-w,y:yf}, {x:x,y:yf})
        this.drawLine(container, 'dashed_line', {x:x,y:yf+hf}, {x:x,y:yf})
    },

    drawLine(container, id, p1, p2){
        p1.x=p1.x==undefined?0:p1.x;
        p1.y=p1.y==undefined?0:p1.y;
        p2.x=p2.x==undefined?0:p2.x;
        p2.y=p2.y==undefined?0:p2.y;
        container.append('line')
            .attr('class', id)
            .attr('x1', p1.x)
            .attr('y1', p1.y)
            .attr('x2', p2.x)
            .attr('y2', p2.y)   
    },

    destroyElements(container, selector){
        container.selectAll(selector).remove();
    }
  }
};
</script>

<style>

#viz{
    background-color: rgb(207, 207, 207);
    width:100%; height:100%;
}

#innerContainer{
    fill: white;
}

.line {
    fill: none;
    stroke-width: 3;
}

.dot {
    stroke: #fff;
}

.dot.founded{
    fill: #ffab00; 
}

.line.founded{
    stroke: #ffab00;
}
.dot.scored{
    fill: #2a7eeb; 
}

.line.scored{
    stroke: #2a7eeb;
}

.dashed_line{
    stroke:black;
    stroke-width:2;
    stroke-dasharray:6 4;  
}

.occurenceLine{
    stroke:rgb(26, 218, 26);
    stroke-width:4;
}

</style>