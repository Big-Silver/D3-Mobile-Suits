# D3(Mobile-Patent-Suit)

<img width="900" src="img/public/d3.png" border="0" />

## About
This project is Mobile-Patent-Suits using D3.js.
This D3(Mobile-Patent-Suit) example is written by [Big Silver].

## Quick Start

```bash
# clone our repo
$ git clone https://github.com/Big-Silver/D3-Mobile-Suits.git D3-Mobile-Suits

# change directory to your app
$ cd D3-Mobile-Suits

# Run the D3.js app.

```
## index.html

```javascript
<html>
<head>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <script type="text/javascript" src="vis.min.js"></script>
    <link href="vis.min.css" rel="stylesheet" type="text/css" />

    <style type="text/css">
        #mynetwork {
            width: 100%;
            height: 100%;
            border: 5px solid lightgray;
        }
        .col-sm-3 {
            padding-top: 50px;
        }
        button {
            width: 80%;
        }
        td {
            border: solid 1px grey;
            padding: 5px 10px;
        }
    </style>
</head>
<body>
    <div class="row">
        <div class="col-sm-3" align="center">
            <button class="btn-warning" onclick="clusterByCid(1)">Cluster all nodes with CID = 1</button>
            <br><br>
            <button class="btn-success" onclick="clusterByColor()">Cluster by color</button>
            <br><br>
            <button class="btn-info" onclick="clusterByConnection()">Cluster 'node 1' by connections</button>
            <br><br>
            <button class="btn-primary" onclick="clusterOutliers()">Cluster outliers</button>
            <br><br>
            <button class="btn-danger" onclick="clusterByHubsize()">Cluster by hubsize</button>
        </div>
        <div class="col-sm-9">
            <div id="mynetwork"></div>        
        </div>
    </div>
    <script type="text/javascript">
        var DIR = "img/";
        // create an array with nodes
        var nodes = [
            {id: 1,  label: 'Node 1', color:'orange', shape:'circularImage', image:DIR + '1.jpg'},
            {id: 2,  label: 'Node 2', color:'DarkViolet', font:{color:'blue'}, shape:'circularImage', image:DIR + '2.jpg'},
            {id: 3,  label: 'Node 3', color:'orange', shape:'circularImage', image:DIR + '1.jpg'},
            {id: 4,  label: 'Node 4', color:'DarkViolet', font:{color:'blue'}, shape:'circularImage', image:DIR + '3.jpg'},
            {id: 5,  label: 'Node 5', color:'orange', shape:'circularImage', image:DIR + '4.jpg'},
            {id: 6,  label: 'cid = 1', cid:1, color:'orange', shape:'circularImage', image:DIR + '5.jpg'},
            {id: 7,  label: 'cid = 1', cid:1, color:'DarkViolet', font:{color:'blue'}, shape:'circularImage', image:DIR + '6.jpg'},
            {id: 8,  label: 'cid = 1', cid:1, color:'lime', shape:'circularImage', image:DIR + '7.jpg'},
            {id: 9,  label: 'cid = 1', cid:1, color:'orange', shape:'circularImage', image:DIR + '8.jpg'},
            {id: 10, label: 'cid = 1', cid:1, color:'lime', shape:'circularImage', image:DIR + '1.jpg'}
        ];

        // create an array with edges
        var edges = [
            {from: 1, to: 2, title: '<table><tr><td>Node1</td><td>-></td><td>Node2</td></tr></table>'},
            {from: 1, to: 3, title: '<table><tr><td>Node1</td><td>-></td><td>Node3</td></tr></table>'},
            {from: 10, to: 4, title: '<table><tr><td>cid1</td><td>-></td><td>Node4</td></tr></table>'},
            {from: 2, to: 5, title: '<table><tr><td>Node2</td><td>-></td><td>Node5</td></tr></table>'},
            {from: 6, to: 2, title: '<table><tr><td>cid1</td><td>-></td><td>Node2</td></tr></table>'},
            {from: 7, to: 5, title: '<table><tr><td>cid1</td><td>-></td><td>Node5</td></tr></table>'},
            {from: 8, to: 6, title: '<table><tr><td>cid1</td><td>-></td><td>cid1</td></tr></table>'},
            {from: 9, to: 7, title: '<table><tr><td>cid1</td><td>-></td><td>cid1</td></tr></table>'},
            {from: 10, to: 9, title: '<table><tr><td>cid1</td><td>-></td><td>cid1</td></tr></table>'}
        ];

        // create a network
        var container = document.getElementById('mynetwork');
        var data = {
            nodes: nodes,
            edges: edges
        };
        var options = {
            nodes:{
                shape: 'dot',
                scaling:{
                    label:{
                        min:8,
                        max:20
                    }
                }
            },
            layout:{
                randomSeed:8
            },
            interaction:{
                navigationButtons: true,
                keyboard: true,
                hoverConnectedEdges: true,
                tooltipDelay: 100        
            }
        }

        // initialize your network!
        var network = new vis.Network(container, data, options);
        network.on("selectNode", function(params) {
            if (params.nodes.length == 1) {
                if (network.isCluster(params.nodes[0]) == true) {
                    network.openCluster(params.nodes[0]);
                }
            }
        });

        function clusterByCid(id) {
            network.setData(data);
            var clusterOptionsByData = {
                joinCondition:function(childOptions) {
                    return childOptions.cid == id;
                },
                clusterNodeProperties: {id:'cidCluster', borderWidth:3, shape:'database'}
            };
            network.cluster(clusterOptionsByData);
        }
        function clusterByColor() {
            network.setData(data);
            var colors = ['orange','lime','DarkViolet'];
            var clusterOptionsByData;
            for (var i = 0; i < colors.length; i++) {
                var color = colors[i];
                clusterOptionsByData = {
                    joinCondition: function (childOptions) {
                        return childOptions.color.background == color; // the color is fully defined in the node.
                    },
                    processProperties: function (clusterOptions, childNodes, childEdges) {
                        var totalMass = 0;
                        for (var i = 0; i < childNodes.length; i++) {
                            totalMass += childNodes[i].mass;
                        }
                        clusterOptions.mass = totalMass;
                        return clusterOptions;
                    },
                    clusterNodeProperties: {id: 'cluster:' + color, borderWidth: 3, shape: 'database', color:color, label:'color:' + color}
                };
                network.cluster(clusterOptionsByData);
            }
        }
        function clusterByConnection() {
            network.setData(data);
            network.clusterByConnection(1)
        }
        function clusterOutliers() {
            network.setData(data);
            network.clusterOutliers();
        }
        function clusterByHubsize() {
            network.setData(data);
            var clusterOptionsByData = {
                processProperties: function(clusterOptions, childNodes) {
                    clusterOptions.label = "[" + childNodes.length + "]";
                    return clusterOptions;
                },
                clusterNodeProperties: {borderWidth:3, shape:'box', font:{size:30}}
            };
            network.clusterByHubsize(undefined, clusterOptionsByData);
        }
    </script>
</body>
</html>

```

