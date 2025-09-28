<div class="language-echarts">// Chart去过的地方
// 注意，不要在最后一行加;号，因为该代码在函数调用内不能加;号
// 修改 chart 高度可通过块菜单&quot;图表&quot;修改
// 更多参数请参考 https://echarts.apache.org/examples/zh/editor.html?c=scatter-map
(async () =&gt; {
    // 加载中国地图js
    await loadScript('https://jsd.onmicrosoft.cn/npm/echarts/map/js/china.js');
    
    // 获取数据
    const cityData = await getData();
    
    // 预先计算所有标签位置，避免递归
    const labelPositions = calculateAllLabelPositions(cityData);
    
    // 返回地图选项
    return {
        title: {
            text: '中国足迹',
            left: 'center'
        },
        tooltip: {
            trigger: 'item',
            formatter: function(params) {
                if (params.data) {
                    return `${params.data.name}&lt;br/&gt;坐标: ${params.data.value[0]}, ${params.data.value[1]}&lt;br/&gt;数值: ${params.data.value[2]}`;
                }
                return params.name;
            }
        },
        geo: {
            map: 'china',
            roam: true,
            zoom: 1.5,
            scaleLimit: {
                min: 1,
                max: 10
            },
            label: {
                emphasis: {
                    show: false
                }
            },
            itemStyle: {
                normal: {
                    areaColor: '#CDCDCD',
                    borderColor: '#FEFFFF'
                },
                emphasis: {
                    areaColor: '#FEFFFF'
                }
            }
        },
        series: [
            {
                name: '城市',
                type: 'scatter',
                coordinateSystem: 'geo',
                data: cityData,
                symbolSize: function(val) {
                    return Math.max(5, Math.sqrt(val[2]) / 5); // 确保最小尺寸
                },
                itemStyle: {
                    color: '#FF6B6B'
                },
                emphasis: {
                    itemStyle: {
                        color: '#FF0000'
                    }
                }
            },
            {
                name: '连接线',
                type: 'lines',
                coordinateSystem: 'geo',
                zlevel: 2,
                effect: {
                    show: true,
                    period: 6,
                    trailLength: 0.7,
                    color: '#fff',
                    symbolSize: 3
                },
                lineStyle: {
                    normal: {
                        color: '#FF6B6B',
                        width: 1.5,
                        opacity: 0.6,
                        curveness: 0.1
                    }
                },
                data: cityData.map((item, index) =&gt; {
                    return {
                        fromName: item.name,
                        toName: item.name,
                        coords: [[item.value[0], item.value[1]], labelPositions[index]]
                    };
                })
            },
            {
                name: '标签',
                type: 'scatter',
                coordinateSystem: 'geo',
                symbolSize: 0,
                label: {
                    show: true,
                    formatter: '{b}',
                    backgroundColor: 'rgba(255, 255, 255, 0.95)',
                    borderColor: '#FF6B6B',
                    borderWidth: 1,
                    borderRadius: 6,
                    padding: [4, 8],
                    shadowBlur: 4,
                    shadowColor: 'rgba(0, 0, 0, 0.1)',
                    shadowOffsetX: 1,
                    shadowOffsetY: 1,
                    color: '#333',
                    fontSize: 10,
                    fontWeight: 'bold'
                },
                itemStyle: {
                    color: 'transparent'
                },
                data: cityData.map((item, index) =&gt; {
                    return {
                        name: item.name,
                        value: [labelPositions[index][0], labelPositions[index][1], item.value[2]]
                    };
                })
            }
        ]
    };
    
    // 计算所有标签位置（非递归方式）- 增加偏移量来加长连接线
    function calculateAllLabelPositions(allData) {
        const positions = [];
        const placedLabels = []; // 存储已放置标签的位置和大小
        
        // 假设标签的尺寸
        const labelWidth = 2.5; // 稍微增大标签宽度
        const labelHeight = 1.2; // 稍微增大标签高度
        
        // 按数值排序，先放置数值大的标签
        const sortedData = [...allData].sort((a, b) =&gt; b.value[2] - a.value[2]);
        
        for (const item of sortedData) {
            const lng = item.value[0];
            const lat = item.value[1];
            
            // 增大基础偏移量来加长连接线
            const baseOffset = 3; // 从2增加到4，加长连接线
            const extendedOffset = 6; // 从3增加到6，加长连接线
            
            // 候选位置 - 使用增大的偏移量
            const candidates = [
                [lng + baseOffset, lat + baseOffset],      // 右上
                [lng + baseOffset, lat - baseOffset],      // 右下
                [lng - baseOffset, lat + baseOffset],      // 左上
                [lng - baseOffset, lat - baseOffset],      // 左下
                [lng, lat + extendedOffset],               // 正上 - 使用更大的偏移
                [lng, lat - extendedOffset],               // 正下 - 使用更大的偏移
                [lng + extendedOffset, lat],               // 正右 - 使用更大的偏移
                [lng - extendedOffset, lat]                // 正左 - 使用更大的偏移
            ];
            
            // 找到最佳位置
            let bestPosition = candidates[0];
            let minOverlap = Infinity;
            
            for (const candidate of candidates) {
                let overlap = 0;
                
                // 检查与所有已放置标签的重叠
                for (const placed of placedLabels) {
                    const dx = Math.abs(candidate[0] - placed.x);
                    const dy = Math.abs(candidate[1] - placed.y);
                    
                    if (dx &lt; labelWidth &amp;&amp; dy &lt; labelHeight) {
                        const overlapX = labelWidth - dx;
                        const overlapY = labelHeight - dy;
                        overlap += (overlapX * overlapY) / (labelWidth * labelHeight);
                    }
                }
                
                if (overlap &lt; minOverlap) {
                    minOverlap = overlap;
                    bestPosition = candidate;
                }
            }
            
            // 如果所有候选位置都有较大重叠，尝试更远的位置
            if (minOverlap &gt; 0.5) {
                const extendedCandidates = [
                    [lng + 8, lat + 8],  // 进一步增大偏移量
                    [lng + 8, lat - 8],
                    [lng - 8, lat + 8],
                    [lng - 8, lat - 8]
                ];
                
                for (const candidate of extendedCandidates) {
                    let overlap = 0;
                    
                    for (const placed of placedLabels) {
                        const dx = Math.abs(candidate[0] - placed.x);
                        const dy = Math.abs(candidate[1] - placed.y);
                        
                        if (dx &lt; labelWidth &amp;&amp; dy &lt; labelHeight) {
                            const overlapX = labelWidth - dx;
                            const overlapY = labelHeight - dy;
                            overlap += (overlapX * overlapY) / (labelWidth * labelHeight);
                        }
                    }
                    
                    if (overlap &lt; minOverlap) {
                        minOverlap = overlap;
                        bestPosition = candidate;
                    }
                }
            }
            
            // 记录已放置的标签
            placedLabels.push({
                x: bestPosition[0],
                y: bestPosition[1],
                width: labelWidth,
                height: labelHeight
            });
            
            // 保存位置
            const originalIndex = allData.findIndex(d =&gt; 
                d.value[0] === item.value[0] &amp;&amp; d.value[1] === item.value[1]
            );
            positions[originalIndex] = bestPosition;
        }
        
        return positions;
    }
    
    // 获取城市坐标数据
    async function getData() {
        let data = [];
        
        try {
            // 查询包含custom-location属性的块
            const blocks = await query(`
                SELECT * 
                FROM blocks
                WHERE id IN (
                    SELECT block_id
                    FROM attributes
                    WHERE name = 'custom-location' 
                )
                LIMIT 50  -- 限制数据量防止过多数据导致性能问题
            `);
            
            // 处理提取的数据
            for (const block of blocks) {
                // 获取该块的custom-location属性值
                const attributes = await query(`
                    SELECT value 
                    FROM attributes 
                    WHERE block_id = '${block.id}' AND name = 'custom-location'
                `);
                
                if (attributes.length &gt; 0) {
                    const attrValue = attributes[0].value;
                    // 解析属性值，格式如：🌱八达岭长城:: [116.016802, 40.356188, 1000]
                    const match = attrValue.match(/(.*):: \[([0-9.]+),\s*([0-9.]+),\s*([0-9.]+)\]/);
                    if (match) {
                        const cityName = match[1].trim();
                        const lng = parseFloat(match[2]);
                        const lat = parseFloat(match[3]);
                        const value = parseFloat(match[4]);
                        
                        data.push({
                            name: cityName,
                            value: [lng, lat, value]
                        });
                    }
                }
            }
        } catch (error) {
            console.error(&quot;获取数据时出错:&quot;, error);
        }
        
        // 如果没有找到数据，使用默认数据
        if (data.length === 0) {
            data = [
                {name: '北京', value: [116.46, 39.92, 2000]},
                {name: '上海', value: [121.48, 31.22, 1800]},
                {name: '杭州', value: [120.19, 30.26, 1000]},
                {name: '广州', value: [113.23, 23.16, 1500]},
                {name: '成都', value: [104.06, 30.67, 1200]}
            ];
        }
        
        return data;
    }
    
    function loadScript(src) {
        return new Promise((resolve, reject) =&gt; {
            // 检查是否已加载
            if (document.querySelector(`script[src=&quot;${src}&quot;]`)) {
                resolve();
                return;
            }
            
            const script = document.createElement('script');
            script.src = src;
            script.onload = resolve;
            script.onerror = reject;
            document.head.appendChild(script);
        });
    }
    
    async function query(sql) {
        try {
            const result = await requestApi('/api/query/sql', { &quot;stmt&quot;: sql });
            if (result.code !== 0) {
                console.error(&quot;查询数据库出错&quot;, result.msg);
                return [];
            }
            return result.data;
        } catch (error) {
            console.error(&quot;查询失败:&quot;, error);
            return [];
        }
    }
    
    async function requestApi(url, data, method = 'POST') {
        try {
            const response = await fetch(url, {
                method: method, 
                body: JSON.stringify(data || {})
            });
            return await response.json();
        } catch (error) {
            console.error(&quot;API请求失败:&quot;, error);
            return { code: -1, msg: error.message };
        }
    }
})()
</div>
<p>‍</p>
<p>‍</p>
<iframe src="/widgets/widget-sample/" data-subtype="widget" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="width: 1651px; height: 780px;"></iframe>
<p>‍</p>
