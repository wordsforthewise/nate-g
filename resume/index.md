---
layout: page
title: The Resume of Nate G.
image:
  feature: weld-break.png
  credit: andre houssney
  creditlink: http://www.jacobsprings.com
comments: false
modified: 2015-11-08
---

<html>
    <head>
        <link rel="stylesheet" type="text/css" href="hm style.css">
    </head>
    <body>
		<p><a href="NathanGeorge_CV.pdf" style="text-align:left;">Plain version</a>,    <a href="/viviresume.html" style="text-align:right;">Printable version</a></p>
        <div id="heading" class="headings">
        <div id="subHead" class="headings">
        <h1 id="name">Nathan George</h1>
        <h2 id="curPos">PhD Chemical Engineer<br/>Programmer/Scientist/Engineer</h2>
        </div>
        <div id="subHead" class="headings">
        <h3 id="phone">402-740-4858 <img class="headImgs" src="Black/Mobile Phone/Mobile Phone_24.png" alt="phone"></h3>
        <h3 id="email">nathancgeorge@gmail.com <img class="headImgs" src="Black/E-mail/E-mail_24.png" alt="phone"></h3>
        <h3 id="website">ngeorge.us <img class="headImgs" src="Black/World Wide Web/World Wide Web_24.png" alt="phone"></h3>
        <h3 id="location">Elkhorn, NE (temp) <img class="headImgs" src="Black/Location/Location_24.png" alt="phone"></h3>
        </div>
        </div>
        <div id="map"></div>
        <div id="timeline"></div>
        <div class="background" id="education">
        <h2 class="eduLabel">Education</h2>
        </div>
        <div class="background" id="experience">
        <h2 class="expLabel">Experience</h2>
        </div>
        <h2 class="sectionLabel">Skills</h2>
        <h3 class="skillsCaption">Based on the <a href="https://en.wikipedia.org/wiki/Dreyfus_model_of_skill_acquisition">Dreyfus model of skill acquisition</a> (1->5 : Novice->Expert)</h3>
        <div id="prog_skills_plot"></div>
        <div id="science_skills_plot"></div>
        <div id="highlights">
        <h2 class="sectionLabel">Highlights</h2>
        <div id="hl1">
        </div>
        <div id="hl2">
        </div>
        </div>
        <div class="other">
        <h2 class="sectionLabel">Other</h2>
        <p class="otherText">2 seasons of <a href="https://youtu.be/wCfd9ZZsP_k?t=1m11s">Blue Knights Drum and Bugle Corps</a> (2006, 2007)
        <br/>
        Numerous children's science education outreach events</p>
        </div>
        
        
        
        <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
        <script type="text/javascript" src="https://www.google.com/jsapi"></script>
        <script src="rainbowvis.js"></script>
        <script type="text/javascript" src="async.js"></script>
        <script type="text/javascript" src="underscore.min.js"></script>
        <script>
             function setEqualHeight(selector, triggerOnInput) {
                //console.log('setting heights equal');
                var elements = $(selector)
                elements.css("height", "auto")
                var max = Number.NEGATIVE_INFINITY;
                $.each(elements, function(index, item) {
	                if ($(item).height() > max) {
		                max = $(item).height()
	                }
                })
                $(selector).css("height", max + "px")
                //console.log('max height:' + max);

                if (!!triggerOnInput) {
	                $(document).on("input", selector, function() {
		                setEqualHeight(selector, false)

	                })
                }

                $(window).resize(function() {
	                setEqualHeight(selector, false)
                })
            }

            var arrowCounter = 0,
            latAvg = 0,
            lngAvg = 0;

            var eduAddr = ["Elkhorn, NE", "Golden, CO", "Santa Barbara, CA"];
            var expAddr = ["Golden, CO", "Golden, CO", "Milpitas, CA"];
            var addresses = eduAddr.concat(expAddr); // [place name, latLng]
            var eduNames = ["Elkhorn High School", "Colorado School of Mines", "University of California, Santa Barbara"]
            var expNames = ["CSM Hydrate Lab", "NREL", "NuvoSun"];
            var eduPos = ["H.S. Degree", "B.S. Chemical Engineering", "PhD Chemical Engineering"]
            var expPos = ["CSM Research Assistant", "NREL Research Assistant", "Solar Cell Device Process Engineer"];
            var eduAwards = [[],
                                [
                                    ["Tau Beta Pi Engineering Honor Society", 2007],
                                    ["Barry M. Goldwater Scholarship", 2008]
                                ],
                                [
                                    ["IGERT Fellowship", 2010],
                                    ["NSF Honorable Mention", 2011]
                                ]
                                ];
            var eduHighlights = [[],
                                [
                                    ["2 Published Papers, <a href='http://www.slideshare.net/NathanGeorge/publist-47053185'>list</a>"],
                                    ["Barry M. Goldwater Scholarship", 2008]
                                ],
                                [
                                    ['14 Published Papers, <a href="http://www.slideshare.net/NathanGeorge/publist-47053185">list</a>'],
                                    ["Mentored/managed 7 undergraduates"],
                                    ["6 oral presentations"],
                                    ["6 poster presentations"],
                                    ["Middle, Elementary, and High School Science Outreach events"]
                                ]
                                ];
            var eduDescr = ["","","Thesis: <a href='http://www.mrl.ucsb.edu/~seshadri/Theses/NathanGeorge2013.pdf'>Correlating long-range order and local structure to the properties of inorganic solids.</a> Synthesized phosphor materials and studied their structure-property relations via advanced characterization methods."];
            var eduMentors = ["", "", "Advisors: Prof. Ram Seshadri and Prof. Brad Chmelka"]
            var eduGPA = ["4.0/4.0 (unweighted)", "3.956/4.0", "3.78/4.0"]
            var eduHighlights = [[],[["Tau Beta Pi Engineering Honor Society", 2007],  ["Barry M. Goldwater Scholarship", 2008]],[["IGERT Fellowship", 2010], ["NSF GRF Honorable Mention", 2011]]]
            var posters = [["Materials Research Outreach Program", "UCSB", "2011, 2012, 2013"], ["Gordon Conference for Solid State Chemistry", "New London, NH", "2012"], ["International Workshop on Materials", "Ras As Khaimah Center for Advanced Materials, UAE", "2011"], ["Materials Research Society Fall Meeting", "Boston, MA", "2010"]] // event, place, year(s)
            var presentations = [["North American Solid-State Chemistry Conference,", "McMaster University, Ontario, Canada", "2011"], ["The 9th International Meeting of Pacific Rim Ceramic Societies", "Cairns, Queensland, Australia", "2011"], ["Institute of Chemistry of Picardy", "Amiens, France", "2011"], ["IGERT winter symposium", "UCSB", "2012"], ["Materials Research Outreach Program", "UCSB", "2013"], ["AICHE conference", "San Francisco, CA", "2013"]] // event, place, year
            var mentees = [["Nick DeCino", 2013], ["Lucie Devys", 2012], ["Jose Carvalho", 2012], ["Courtney Doll", 2011], ["Lucy Darago", 2010-2011], ["Adam Jaffe", 2010], ["Rory Barker", 2010]]
            var expDescr = ["Assisted with research of inter-particle adhesion forces of hydrate particles in order to better understand plugging and flow behavior of oil and gas pipelines.", "Assisted with research concerning water vapor transport and mechanical properties of transparent conductive oxide (TCO) layers for use in flexible electronics. Also fabricated thin-film TCO samples for testing and analysis using a high-vacuum apparatus.", "Responsible for upkeep, running, and improving the sputtering processes for manufacturing CIGS solar cells. Big data analysis and correlation to experimental conditions (using python and JMP), automation through programming, machine vision, and building software/hardware improvements for the manufacturing line."]
            var expMentors = ["Mentors: Carolyn Koh and Dendy Sloan", "Mentors: Lin Simpson and Arrelaine Dameron", "Managers: Don Person and Art Wall"]
            var eduDates = [[new Date([2001,7,15]), new Date(2005,4,1)],
                       [new Date([2005,7,1]), new Date(2009,4,1)],
                       [new Date([2009,8,1]), new Date(2013,11,20)]]
            var expDates = [[new Date([2007,8,1]), new Date(2008,7,1)],
                            [new Date([2008,8,1]), new Date(2009,5,1)],
                            [new Date([2014,6,1]), new Date(2015,7,12)]]; // [start, end date]
            var dates = eduDates.concat(expDates);
            var descriptions = eduDescr.concat(expDescr);
            var positions = eduPos.concat(expPos);
            var names = eduNames.concat(expNames);
            var mentors = eduMentors.concat(expMentors);
            var datesAndLocs = _.zip(addresses, dates, descriptions, positions, names, mentors);
            datesAndLocs = datesAndLocs.sort(function(d1,d2) { return d1[1][0]-d2[1][0]; }); // sort by start date, newest is last in array
            var consolidatedAddrs = [];
            var highlights = [["Tau Beta Pi Engineering Honor Society", 2007],  ["Barry M. Goldwater Scholarship", 2008], ["IGERT Fellowship", 2010], ["NSF GRF Honorable Mention", 2011]];
            var hl2 = [["6 oral presentations", "2011-2013"], ["4 poster presentations", "2010-2013"], ["Mentored 7 undergraduates", "2010-2013"], ["16 scientific publications", "2009-2014", "http://www.slideshare.net/NathanGeorge/publist-47053185"]];
            var bounds;
            
            //google.load("visualization", "1", {packages:["timeline"]});
            google.charts.load( '43', {packages: ['timeline', 'bar']});
          
            /* skills bar charts */
            var progData = [["Techincal Writing/Communications", 5], ["Python", 4.5], ["C++, C#, C", 2.5], ["ESP8266/Lua IoT Devices", 4], ["Linux/bash", 3.5], ["MS Office", 4], ["Latex", 4], ["JMP", 3], ["HTML", 4], ["Matlab", 3], ["Mathematica", 2], ["Visual Basic", 2], ["Javascript", 3], ["Machine Vision", 3]];
            labels1 = [['Skill', 'Self rating']];
            
            var scienceData = [["High-temp solid-state chemistry", 5], ["Solid-state NMR", 4], ["X-ray/Nuetran diffraction and anaylisis", 4], ["Photoluminescence", 4], ["Quantum yield", 4], ["XANES/EXAFS", 4], ["ESR", 4], ["SEM", 3], ["OES", 3.5], ["Sputter Deposition", 3]];
            labels2 = [['Skill', 'Self rating']];
		
            var progArray = labels1.concat(progData.sort(function(a,b){
                return b[1] - a[1];
                })
            );
            
            var scienceArray = labels2.concat(scienceData.sort(function(a,b){
                return b[1] - a[1];
                })
            );

            function drawProgSkills() {
                // draws the skills chart
                var prog_data = new google.visualization.arrayToDataTable(progArray);
                var prog_options = {
                fontName: 'bebas',
                hAxis: {
                    textStyle: {
                        fontName: 'bebas',
                        fontSize: 14
                    },
                    titleTextStyle: {
                        fontName: 'bebas',
                        fontSize: 14
                    },
                    viewWindow: {min: 0, max: 5}, 
                    ticks: [0,1,2,3,4,5]
                },
                vAxis: {
                    textStyle: {
                        fontName: 'bebas',
                        fontSize: 14
                    },
                    titleTextStyle: {
                        fontName: 'bebas',
                        fontSize: 14 }
                },
                annotations: {
                    textStyle: {
                        fontName: 'bebas',
                        fontSize: 18,
                    }
                },
                legend: { position: 'none', textStyle: {fontName: 'bebas'} },
                tooltip: {textStyle: {fontName: 'bebas'} },
                chart: {},
                bars: 'horizontal', // Required for Material Bar Charts.
                axes: {
                x: {
                  0: { side: 'top', label: 'Programming/Tech Self rating'} // Top x-axis.
                }
                },
                bar: { groupWidth: "90%" }
                };

                var prog_chart = new google.charts.Bar(document.getElementById('prog_skills_plot'));
                prog_chart.draw(prog_data, google.charts.Bar.convertOptions(prog_options));
                
                
                var sci_data = new google.visualization.arrayToDataTable(scienceArray);
                var sci_options = {
                fontName: 'bebas',
                hAxis: {
                    textStyle: {
                        fontName: 'bebas',
                        fontSize: 14
                    },
                    titleTextStyle: {
                        fontName: 'bebas',
                        fontSize: 14
                    },
                    viewWindow: {min: 0, max: 5}, 
                    ticks: [0,1,2,3,4,5]
                },
                vAxis: {
                    textStyle: {
                        fontName: 'bebas',
                        fontSize: 14
                    },
                    titleTextStyle: {
                        fontName: 'bebas',
                        fontSize: 14 }
                },
                annotations: {
                    textStyle: {
                        fontName: 'bebas',
                        fontSize: 18,
                    }
                },
                legend: { position: 'none', textStyle: {fontName: 'bebas'} },
                tooltip: {textStyle: {fontName: 'bebas'} },
                chart: {},
                bars: 'horizontal', // Required for Material Bar Charts.
                axes: {
                x: {
                  0: { side: 'top', label: 'Science Skills Self rating'} // Top x-axis.
                }
                },
                bar: { groupWidth: "90%" }
                };

                var sci_chart = new google.charts.Bar(document.getElementById('science_skills_plot'));
                sci_chart.draw(sci_data, google.charts.Bar.convertOptions(sci_options));
            }
            
            function drawScienceSkills() {
                // draws the skills chart
                
            }

            var theColors;

            function drawTimeline() {
                drawProgSkills();
                drawScienceSkills();
                var container = document.getElementById('timeline');
                var chart = new google.visualization.Timeline(container);
                var dataTable = new google.visualization.DataTable();

                dataTable.addColumn({ type: 'string', id: 'label' });
                dataTable.addColumn({ type: 'string', id: 'Location' });
                dataTable.addColumn({ type: 'date', id: 'Start' });
                dataTable.addColumn({ type: 'date', id: 'End' });
                dataTable.addRows(allData);
                var options = {
                    timeline: { showRowLabels: true, barLabelStyle: { fontName: 'bebas', fontSize: 14 } },
                    colors: eduColors.concat(expColors),
                  };
                chart.draw(dataTable, options);
            }
            
            // assign gradient of colors to places, one for each unique place

            function onlyUnique(value, index, self) { 
                return self.indexOf(value) === index;
            }
            var uniquePlaces = addresses.filter( onlyUnique );
            var rainbow = new Rainbow(); // by default, range is 0 to 100
            rainbow.setSpectrum('blue', 'red');
            rainbow.setNumberRange(1, uniquePlaces.length);
            
            var spotColors = [];
            for (var i = 1; i <= uniquePlaces.length; i++) {
                spotColors.push(rainbow.colourAt(i));
            }

            var allData = [], eduColors = [], expColors = [];
            datesAndLocs.forEach(function(e,i,arr) {
              if (eduDates.indexOf(e[1]) != -1 ) {
                allData.push(["Education", eduPos[eduDates.indexOf(e[1])], e[1][0], e[1][1]]);
                eduColors.push(spotColors[uniquePlaces.indexOf(e[0])]);
              } else if (expDates.indexOf(e[1]) != -1 ) {
                allData.push(["Experience", expPos[expDates.indexOf(e[1])], e[1][0], e[1][1]]);
                expColors.push(spotColors[uniquePlaces.indexOf(e[0])]);
              }
            });  
            
            var geocoder, map;
            var clustMarkers, markers = [];
            
            function initMap() {
              geocoder = new google.maps.Geocoder();
              
              
              var myLatLng = {lat: 39.8282, lng: 98.5795}; // geographic center of the US
              bounds = new google.maps.LatLngBounds();
              map = new google.maps.Map(document.getElementById('map'), {
                zoom: 5,
                center: myLatLng,
                    scrollwheel: false,
                    draggable: true,
                    disableDefaultUI: true
              });
              
              var oms = new OverlappingMarkerSpiderfier(map,
                {markersWontMove: true, markersWontHide: true, keepSpiderfied: true});
              
              function openAllClusters() {
                clustMarkers = oms.markersNearAnyOtherMarker();
                $.each(clustMarkers, function (i, marker) {
                    google.maps.event.trigger(marker, 'click');
                });
                $.each(markers, function (i, marker) {
                    marker.addListener('click', function() {
                        marker.infowindow.open(map, marker);
                    });
                });
              }
              
              async.forEachOf(datesAndLocs, function(e, i, callback) {
                //console.log(e[0]);
                // draw all map points, then draw arrows between locations, center map, and fit-zoom
                if (datesAndLocs[i-1] === undefined) {
                    consolidatedAddrs.push([e[0]]);
                } else {
                    if (datesAndLocs[i-1][0] == e[0]) {
                        // do nothing
                    } else {
                        consolidatedAddrs.push([e[0]]);
                    }
                }
                geocoder.geocode({'address': e[0]}, function(results, status) {
                var pinColor;
                if (status === google.maps.GeocoderStatus.OK) {
                    if (datesAndLocs[i-1] === undefined) {
                            pinColor = spotColors[0];
                            consolidatedAddrs[i].push(results[0].geometry.location);
                        } else {
                            if (datesAndLocs[i-1][0] == e[0]) {
                            } else {
                                consolidatedAddrs.forEach(function(Ce, Ci, Ca) {
                                if (Ce[0] == e[0] && Ce[1] === undefined) { consolidatedAddrs[Ci].push(results[0].geometry.location); }
                                });
                            }
                            consolidatedAddrs.forEach(function(Ce, Ci, Ca) {
                                    if (Ce[0] == e[0]) {pinColor = spotColors[Ci];}
                            });
                    }
                    var pinImage = new google.maps.MarkerImage("http://chart.apis.google.com/chart?chst=d_map_pin_letter&chld=%E2%80%A2|" + pinColor,
                    new google.maps.Size(21, 34),
                    new google.maps.Point(0,0),
                    new google.maps.Point(10, 34));
                    var marker = new google.maps.Marker({
                        map: map,
                        position: results[0].geometry.location,
                        icon: pinImage
                    });
                    markers.push(marker);
                    oms.addMarker(marker);
                    bounds.extend(results[0].geometry.location);
                    
                    // add infowindow
                    var contentString = '<div id="content">' + '<div id="siteNotice">' + '<h3 class="infoName">' + e[4] + ' <span class="bl">in</span> ' + e[0] + '</h3>' + '<h3 class="infoTitle">' + e[3] + ' <span class="bl">from</span> ' +  e[1][0].getFullYear() + '-' + e[1][1].getFullYear() + '</h3>' + '<p class="infoDescr">' + e[2] + '</p><p class="infoBoss">' + e[5] + '</p></div></div>'
                    marker.infowindow = new google.maps.InfoWindow({
                        content: contentString
                    });
                    marker.infowindow.addListener('closeclick', function() {
                        centerTheMap();
                    });
                    
                    // calculate average lattitude and longitude
                    latAvg += results[0].geometry.location.lat();
                    lngAvg += results[0].geometry.location.lng();
                    //datesAndLocs[i].push(results[0].geometry.location);
                    arrowCounter += 1;
                    return callback(); // seems like it tells async.forEachOf that the function is done
                } else {
                    alert('Geocode was not successful for the following reason: ' + status);
                }
                });
              }, function(err) {
                map.fitBounds(bounds);
                if (err) {
                    console.log("error: " + err);
                } else {
                latAvg = latAvg/addresses.length;
                lngAvg = lngAvg/addresses.length;
                centerTheMap();
                drawAllArrows();
                fillPosInfo();
                setEqualHeight(".background", true);
                google.setOnLoadCallback(drawTimeline);
                }
                google.maps.event.addListener(map, 'idle', openAllClusters());
              });
            }
            
            function fillPosInfo() {
                    eduPos.forEach(function(e, i, arr) {
                        var eduDiv = document.createElement("div");
                        eduDiv.setAttribute("id", e);
                        eduDiv.setAttribute("class", "anEdu");
                        document.getElementById("education").appendChild(eduDiv);
                        var school = document.createElement("p");
                        school.textContent = eduNames[i] + ": " + eduDates[i][0].getFullYear() + '-' + eduDates[i][1].getFullYear();
                        var place = document.createElement("p");
                        place.textContent = eduAddr[i];
                        var degree = document.createElement("p");
                        degree.textContent = e + " (GPA: " + eduGPA[i] + ")";
                        //var years = document.createElement("p");
                        //years.textContent = eduDates[i][0].getFullYear() + '-' + eduDates[i][1].getFullYear();
                        var descr = document.createElement("p");
                        descr.textContent = eduDescr[i];
                        var br = document.createElement("br");
                        school.setAttribute("class", "schoolHead");
                        degree.setAttribute("class", "degree");
                        place.setAttribute("class", "eduAddr");
                        //years.setAttribute("class", "schoolYears");
                        descr.setAttribute("class", "schoolDescr");
                        document.getElementById(e).appendChild(school);
                        document.getElementById(e).appendChild(degree);
                        document.getElementById(e).appendChild(place);
                        //document.getElementById("education").appendChild(years);
                        document.getElementById(e).appendChild(br);
                        
                        /*descr.setAttribute("style", "display:none; position:absolute;");
                        document.getElementById(e).appendChild(descr);
                        // show/hide more details on mouseover
                        $(".anEdu").mouseover(function() {
                            $(this).children(".schoolDescr").show();
                        }).mouseout(function() {
                            $(this).children(".schoolDescr").hide();
                        });*/
                    });
                    expPos.forEach(function(e, i, arr) {
                        var company = document.createElement("p");
                        company.textContent = expNames[i] + ": " + expDates[i][0].getFullYear() + '-' + expDates[i][1].getFullYear();
                        var place = document.createElement("p");
                        place.textContent = expAddr[i];
                        var position = document.createElement("p");
                        position.textContent = e;
                        //var years = document.createElement("p");
                        //years.textContent = expDates[i][0].getFullYear() + '-' + expDates[i][1].getFullYear();
                        var descr = document.createElement("p");
                        descr.textContent = expDescr[i];
                        var br = document.createElement("br");
                        company.setAttribute("class", "expHead");
                        position.setAttribute("class", "position");
                        place.setAttribute("class", "expAddr");
                        //years.setAttribute("class", "expYears");
                        descr.setAttribute("class", "expDescr");
                        document.getElementById("experience").appendChild(company);
                        document.getElementById("experience").appendChild(position);
                        document.getElementById("experience").appendChild(place);
                        //document.getElementById("experience").appendChild(years);
                        document.getElementById("experience").appendChild(br);
                    });
                    highlights.forEach(function(e, i, arr) {
                        var hL = document.createElement("p");
                        hL.textContent = e[0] + ": " + e[1];
                        hL.setAttribute("class", "resHighlight");
                        document.getElementById("hl1").appendChild(hL);
                    });
                    hl2.forEach(function(e, i, arr) {
                        if (e[2] === undefined) {
                            var hL = document.createElement("p");
                            hL.textContent = e[0] + ": " + e[1];
                            hL.setAttribute("class", "resHighlight2");
                            document.getElementById("hl2").appendChild(hL);
                        } else {
                            var hLlink = document.createElement("a");
                            hLlink.title = e[0];
                            hLlink.href = e[2];
                            var linkText = document.createTextNode(e[0]);
                            hLlink.appendChild(linkText);
                            var hL = document.createElement("p");
                            var linkDate = document.createTextNode(": " + e[1]);
                            hL.setAttribute("class", "resHighlight2");
                            hL.setAttribute("id", e[0]);
                            document.getElementById("hl2").appendChild(hL).appendChild(hLlink);
                            document.getElementById(e[0]).appendChild(linkDate);
                        }
                        document.getElementById("hl2").appendChild(hL);
                    });
                    }

            function centerTheMap() {
                        //console.log('setting map center');
                        //console.log(latAvg);
                        //console.log(lngAvg);
                        var centerPosition = new google.maps.LatLng(latAvg, lngAvg);
                        //console.log(centerPosition);
                        map.setCenter(centerPosition);
            }
            
            function drawAllArrows() {
                if (arrowCounter == datesAndLocs.length) {
                    consolidatedAddrs.forEach(function(e, i, ar) {  
                        if (consolidatedAddrs[i - 1] === undefined) {
                            console.log('not drawing arrow');
                        }
                        else {
                            console.log('drawing arrow from');
                            console.log(consolidatedAddrs[i-1][0]);
                            console.log('to');
                            console.log(e[0]);
                            drawArrow(consolidatedAddrs[i-1][1], e[1], map);
                        }
                    });
                }
            }

            function drawArrow(startPt, endPt, resultsMap) {
                var lineSymbol = {
                    path: google.maps.SymbolPath.FORWARD_CLOSED_ARROW
                };

                // Create the polyline and add the symbol via the 'icons' property.
                var line = new google.maps.Polyline({
                path: [startPt, endPt],
                icons: [{
                    icon: lineSymbol,
                    offset: '52%' // the head of the arrow is placed where the offset is, roughly centers the arrow on the line
                    }],
                    map: resultsMap
                });
            };
            
    </script>
    <script src="jquery-1.11.3.min.js"></script>
    <script async defer
        src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCjC3wfeMTg2FHqWpWXTMiFfp61Ofpxwhg&signed_in=false&callback=loadOMS">
    </script>
    <script>
            function loadOMS() {
                jQuery.get('oms.min.js', function(data) {
                    initMap();
                    setEqualHeight(".headings", true);
                });
            }
</script>
    </body>
</html>