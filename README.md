# techhounds$(function() {

	
	$.post("/usage/rest/metrics/getDefaultApplicationOption", function( dataVal ) {
		var data = dataVal;
		var dropdown = $('#application');
		$(dropdown).empty();
		$(dropdown).append($('<option/>', {
	        value: "select",
	        text : "Select"
	    }));
		$(data).each(function (i) {
			$(dropdown).append($('<option/>', {
		        value: data[i].applicationName,
		        text : data[i].applicationName
		    }));
		});
		$(dropdown).trigger('update');

		var data1 = dataVal;
		var dropdown = $('#applicationServer');
		$(dropdown).empty();
/*		$(dropdown).append($('<option/>', {
	        value: "select",
	        text : "Select"
	    }));*/
		$(data1).each(function (i) {
			$(dropdown).append($('<option/>', {
		        value: data1[i].applicationName,
		        text : data1[i].applicationName
		    }));
		});
		$(dropdown).trigger('update');
		});
		timePicker('#startdate','#enddate');
		timePicker('#startdateServer','#enddateServer');
	findInitGraph();
});

function findInitGraph()
{
	$.post("/usage/rest/metrics/getOverviewChart", function( dataVal ) {
		pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),"Application Vs No of Hits");
		$('#appGridData').html(drawPieTable("Application","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
		pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Application Vs No of Users");
		$('#userGridData').html(drawPieTable("Application","","User Number",jQuery.parseJSON(dataVal.userSide)));
		
	});
}
function intiServerGraph()
{
	$.post("/usage/rest/metrics/findServerVsNoOfHitsForOverview", function( dataVal ) {
		pieChart('#pieContaineruserModuleServerRight',consolidataArrAjax(jQuery.parseJSON(dataVal)),"Server Vs No of Hits");
//		$('#serverGridData').html(drawPieTable("Server","","Hits",jQuery.parseJSON(dataVal)));
	});
}


function timePicker(startDate,endDate){
	var d = new Date();
	var month="";
	var year="";
	if(d.getMonth()== 0){
	month=12;
	year=d.getFullYear() - 1;
	}
	else{
	month=d.getMonth();
	year=d.getFullYear();
	}

	$(startDate).val(("00" + (month)).slice(-2) + "/" +
	    ("00" + d.getDate()).slice(-2) + "/" + year + " " +
	    "12:00 am");

	$(endDate).val(("00" + (d.getMonth() + 1)).slice(-2) + "/" +
	    ("00" + d.getDate()).slice(-2) + "/" + d.getFullYear() + " " +
	    "11:59 pm");

    $(startDate).datetimepicker({
		altFormat: "MM/dd/yyyy hh:mm",
		buttonImage: 'http://TUSFL000VM11421.vdsi.ent.verizon.com:8081/artifactory/vzWebKit/images/1.0/calendar.png',
        buttonImageOnly: true,
		timeFormat: "hh:mm tt",
		showOn: 'both',
		changeMonth : true,
		changeYear : true,
		});
    $(endDate).datetimepicker({
		altFormat: "MM/dd/yyyy hh:mm",
		buttonImage: 'http://TUSFL000VM11421.vdsi.ent.verizon.com:8081/artifactory/vzWebKit/images/1.0/calendar.png',
        buttonImageOnly: true,
		timeFormat: "hh:mm tt",
		showOn: 'both',
		changeMonth : true,
		changeYear : true,
		});

		}

function barChart(chartArr,msg,ele)
{
	Highcharts.setOptions({
		colors: ['#ffc000', '#00b050', '#ea700d', '#c00000', '#400000 ', '#660066', '#FF3366', '#0000FF' , '#6600FF', '#6610FF', '#6620FF', '#6630FF', '#6640FF', '#6650FF']
	});
  $(ele).highcharts({
        chart: {
			renderTo: 'barcontainer',
			 type: 'bar',
			cursor: 'pointer',
			style: {
       				 fontFamily: 'Arial',
        			color: "black"
    	}


        },
        title: {
            text: msg
        },
		 credits: {
	 		 enabled: false
		 },
        xAxis: {

			lineColor: 'transparent',
				tickLength: 0,
			plotBorderColor: '#346691',
            categories: chartArr[0],
			labels: {
     			style: {
        					color: '#000',
        					font: '12px Arial',
							fontWeight: 'bold',
     					}
			},
        },

     	 yAxis: {
			gridLineWidth: 0,
            labels:
				{
						enabled: false
				}
        },

		tooltip: {
            formatter: function() {
                return '<b>'+ this.x +'</b><br/>'+
                    this.series.name +': '+ this.y +'<br/>';
            }
        },

        legend: {
            enabled: false
        },


        plotOptions: {
            series: {
				pointWidth: 15,

                stacking: 'normal',
				dataLabels: {
                    enabled: false,
                    },
                style: {
              	width:'32%'
              },
				events: {
           		 legendItemClick: function(event) {
                if (!this.visible)
                    return true;
            }
        }
            },



        },


          series: chartArr[1]


    });
}



function barGraph(chartArr,msg,ele)
{

	Highcharts.setOptions({
		colors: ['#ffc000', '#00b050', '#ea700d', '#c00000', '#400000 ', '#660066', '#FF3366', '#0000FF' , '#6600FF', '#6610FF', '#6620FF', '#6630FF', '#6640FF', '#6650FF']
	});

	$(ele).highcharts({
        chart: {
            type: 'column'
        },
        title: {
            text: msg
        },
        xAxis: {
            categories: chartArr[0],
            tickWidth: 0  ,
            labels: {
                rotation: -90

        }

        },
        yAxis: {
            min: 0,
            lineWidth: 1,
            gridLineDashStyle: 'longdash',

            title: {
                text: null
            },
            stackLabels: {
                enabled: false,
                style: {
                    fontWeight: 'bold',
                    color: (Highcharts.theme && Highcharts.theme.textColor) || 'gray'
                }
            }
        },

        tooltip: {
            formatter: function() {
                return '<b>'+ this.x +'</b><br/>'+
                    this.series.name +': '+ this.y +'<br/>'+
                    'Total: '+ this.point.stackTotal;
            }
        },
        plotOptions: {
            column: {
                stacking: 'normal',
                dataLabels: {
                    enabled: false,
                    color: (Highcharts.theme && Highcharts.theme.dataLabelsColor) || 'white',
                    style: {
//                        textShadow: '0 0 3px black, 0 0 3px black'
                    	width:'32%'
                    }
                },
                showInLegend: true
            }
        },
        credits :{ enabled: false},
        series: chartArr[1]
    });


}

function lineGraph(chartArr,msg,ele)
{
	Highcharts.setOptions({
	    colors: ['#ffc000', '#00b050', '#ea700d', '#c00000', '#400000 ', '#660066', '#FF3366', '#0000FF' , '#6600FF', '#6610FF', '#6620FF', '#6630FF', '#6640FF', '#6650FF']
	});

	ele.highcharts({
        chart: {
            type: 'line'
        },
        title: {
            text: msg
        },
        tooltip: {
            formatter: function() {
                return '<b>'+ this.x +'</b><br/>'+
                    this.series.name +': '+ this.y ;
            }
        },

        xAxis: {
            categories: chartArr[0],
            tickWidth: 0  ,
            labels: {
            rotation: -90
       			 }
        },
        yAxis: {
            title: {
                text: null
            }
        },
        plotOptions: {
            line: {
                dataLabels: {
                    enabled: false
                },
                enableMouseTracking:true,
               showInLegend: true,
               style: {
             	width:'32%'
             }
            }

        },
        credits :{
                    enabled: false
                },

        series:chartArr[1]
    });

}


function pieChart(ele,resultArr,msg){
	console.log(msg);
	
	Highcharts.setOptions({
		colors: ['#ffc000', '#00b050', '#ea700d', '#c00000', '#400000 ', '#660066', '#FF3366', '#0000FF' , '#6600FF', '#6610FF', '#6620FF', '#6630FF', '#6640FF', '#6650FF']
	});

					 $(''+ele).highcharts({
	        chart: {
	        	plotBackgroundColor: null,
	            plotBorderWidth: null,
	            plotShadow: false
	        },
	         title:{
				 text : msg
				 },
	        tooltip: {
	    	    pointFormat: '{series.name}: <b>{point.percentage:.0f}%</b>'
	        },
			  credits: {
			 		 enabled: false
				 },

	        plotOptions: {
	        	pie: {
	        		 cursor: 'pointer',
	                dataLabels: {
	                    enabled: true,
	                    format: '<b> {point.percentage:.0f} % </b>',
	                    style: {
//	                        textShadow: '0 0 3px black, 0 0 3px black'
	                    	color: (Highcharts.theme && Highcharts.theme.contrastTextColor) || 'black',
	                    	width:'32%'
	                    }
	                },
	                showInLegend: true
	            }
	        },
	        series: [{
	            type: 'pie',
	            name: 'Task Overview',
	            data: resultArr,
	        }]
	    });

}


function onApplicationChange()
{
	$.ajax({
		url:"/usage/rest/metrics/getModuleOption",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
		success:function(result){
			var data = result;
			var dropdown = $('#module');
			$(dropdown).empty();
			$(dropdown).append($('<option/>', {
		        value: "select",
		        text : "Select"
		    }));
			$(data).each(function (i) {
				$(dropdown).append($('<option/>', {
			        value: data[i].moduleName,
			        text : data[i].moduleName
			    }));
			});
			$(dropdown).trigger('update');
			$(".component .value .fancy-select .options li").css("width","113px");

	   }
	});

}


function submitMetrics()
{
	setDefaultGrid();
	if($('#application').val() == 'select' && $('#module').val() == 'select'  )
	{
		//teamTasksCustom("default","","","");
		$('.datainfo_metrics').css('width','48%');
		$('#perfOver').change();
		$('#rightGraphStyle').change();
		$('#task_list_screen').hide();
		$(".stylec").hide();

	}
	else if($('#application').val() != 'select' && $('#module').val() == 'select'  )
	{
//		teamTasksCustom("application",$('#application').val(),"","");
		$('.datainfo_metrics').css('width','48%');
		$('#perfOver').change();
		$('#rightGraphStyle').change();
		$('#task_list_screen').hide();
		$(".stylec").hide();

	}
	else if($('#application').val() != 'select' && $('#module').val() != 'select' )
	{
//		teamTasksCustom("module",$('#module').val(),$('#application').val(),"");
		$('.datainfo_metrics').css('width','32%');
		$('#perfOver').change();
		$('#rightGraphStyle').change();
		  $('#botGraphStyle').change();
		$('#task_list_screen').show();
		$('#detail_task_avg').css('display','block');
		$('#detail_task_avg').show();
		$(".stylec").hide();

	}
	else
	{
	$('#detail_task_avg').hide();
	}
}


function teamTasksCustom(category,id,id1,id2)
{
/*	$('#pieContainer').show();
	$('#barContainer').hide();
	$('#lineContainer').hide();*/
	if(category == "default")
	{
		$(".stylec").hide();
		$('.datainfo_metrics').css('width','48%');
		if(appGridView ==0)
			{
			getAllAppVsNoOfHitsDefault("Application Vs No of Hits",id);
			}
		else
			{
			getAllAppVsNoOfHitsDefaultGrid("Application Vs No of Hits",id);
			}

	}
	else if(category == "application")
	{
		$(".stylec").hide();
		$('.datainfo_metrics').css('width','48%');
		if(userGridView ==0)
		{
		getAllModVsNoOfHits("Module Vs No of Hits",id);
		}
		else
			{
			getAllModVsNoOfHitsGrid("Module Vs No of Hits",id);
			}
	}
	else if(category == "module")
	{
		$('#detail_task_avg').css('display','block');
		$('#detail_task_avg').show();
		$(".stylec").hide();
		$('.datainfo_metrics').css('width','32%');
		if(screenGridView  ==0)
		{
		getAllScreenVsNoOfHits("Screen Vs No of Hits",id,id1);
		}
		else
			{
			getAllScreenVsNoOfHitsGrid("Screen Vs No of Hits",id,id1);
			}
	}
	else if(category == "screen")
	{
		$('#detail_task_avg').css('display','block');
		$('#detail_task_avg').show();
		$(".stylec").hide();
	}

}

function getAllAppVsNoOfHitsDefault(msg,id)
{
	$.ajax({
		url:"/usage/rest/metrics/getOverviewChartWithDate",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val()},
		success:function(dataVal){
			pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),msg);
			pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Application Vs No of Users");
		}
	});
}

function getAllAppVsNoOfHitsDefaultGrid(msg,id)
{
	$.ajax({
		url:"/usage/rest/metrics/getOverviewChartWithDate",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val()},
		success:function(dataVal){
			$('#appGridData').html(drawPieTable("Application","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
			$('#userGridData').html(drawPieTable("Application","","User Number",jQuery.parseJSON(dataVal.userSide)));
		}
	});
}


function getAllModVsNoOfHits(msg,id)
{
	$.ajax({
		url:"/usage/rest/metrics/getOverviewApplicationChart",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
		success:function(dataVal){
			pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),msg);
			pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Module Vs No of Users");
		}
	});
}

function getAllModVsNoOfHitsGrid(msg,id)
{
	$.ajax({
		url:"/usage/rest/metrics/getOverviewApplicationChart",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
		success:function(dataVal){
			$('#appGridData').html(drawPieTable("Module","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
			$('#userGridData').html(drawPieTable("Module","","User Number",jQuery.parseJSON(dataVal.userSide)));
		}
	});
}

function getAllScreenVsNoOfHits(msg,id1,id)
{
	$.ajax({
		url:"/usage/rest/metrics/getOverviewModuleChart",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
		success:function(dataVal){
			pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),msg);
			pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Screen Vs No of Users");
			pieChart('#pieContaineruserScreen',consolidataArrAjax(jQuery.parseJSON(dataVal.responseSide)),"Averege Response Time Vs Hits");
		}
	});
}

function getAllScreenVsNoOfHitsGrid(msg,id1,id)
{
	$.ajax({
		url:"/usage/rest/metrics/getOverviewModuleChart",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
		success:function(dataVal){
			$('#appGridData').html(drawPieTable("Screen","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
			$('#userGridData').html(drawPieTable("Screen","","User Number",jQuery.parseJSON(dataVal.userSide)));
			$('#screenGridData').html(drawPieTable("Averege Response Time","","Hits",jQuery.parseJSON(dataVal.responseSide)));
		}
	});
}

function preftabChange(ele)
{
	if (appGridView == 0)
	{
	if($(ele).val()=="weekly"){
	$('#appGraphData').show();
	$('#appGridData').hide();
	$('#pieContainer').hide();
	$(".stylec").show();
	$('#barContainer').hide();
	$('#leftGraphStyle').parent().parent().show();
	$('.headercontent').show();
	$('#leftGraphStyle').parent().show();
	$('#leftGraphStyle').val('line');
	$('#leftGraphStyle').change();
	$('#lineContainer').hide();
	$('#weeklineContainer').show();
	$('#weekdashContainer').hide();
	$('#monthbarContainer').hide();
	$('#monthdashContainer').hide();
	}
	else if($(ele).val()=="monthly"){
		 $('#appGraphData').show();
		$('#appGridData').hide();
		$('#pieContainer').hide();
		$(".stylec").show();
	$('#barContainer').hide();
	$('#lineContainer').show();
	$('#weeklineContainer').hide();
	$('#weekdashContainer').hide();
	$('#monthbarContainer').hide();
	$('#monthdashContainer').hide();
	$('#leftGraphStyle').parent().parent().show();
	$('.headercontent').show();
	$('#leftGraphStyle').parent().show();
	$('#leftGraphStyle').val('line');
	$('#leftGraphStyle').change();
		}
		 else {
			 $('#appGraphData').show();
		$('#appGridData').hide();
		$(".stylec").hide();
    $('#pieContainer').show();
	$('#barContainer').hide();
	$('#lineContainer').hide();
	 $('#weeklineContainer').hide();
		$('#weekdashContainer').hide();
		$('#monthbarContainer').hide();
		$('#monthdashContainer').hide();
		$('#leftGraphStyle').parent().hide();
		$('#headercontent').show();
		drawMultiGraphsApp("overview");
			 }
	}
	else
		{
		if($(ele).val()=="weekly"){
			$('#appGraphData').hide();
			$('#appGridData').show();
			$('#pieContainer').hide();
			$(".stylec").show();
			$('#barContainer').show();
			$('#leftGraphStyle').parent().parent().show();
			$('.headercontent').show();
			$('#leftGraphStyle').parent().show();
			$('#leftGraphStyle').val('bar');
			$('#leftGraphStyle').change();
			$('#lineContainer').hide();
			$('#weeklineContainer').hide();
			$('#weekdashContainer').hide();
			$('#monthbarContainer').hide();
			$('#monthdashContainer').hide();
			}
			else if($(ele).val()=="monthly"){
				 $('#appGraphData').hide();
				$('#appGridData').show();
				$('#pieContainer').hide();
				$(".stylec").show();
			$('#barContainer').hide();
			$('#lineContainer').show();
			$('#weeklineContainer').hide();
			$('#weekdashContainer').hide();
			$('#monthbarContainer').hide();
			$('#monthdashContainer').hide();
			$('#leftGraphStyle').parent().parent().show();
			$('.headercontent').show();
			$('#leftGraphStyle').parent().show();
			$('#leftGraphStyle').val('line');
			$('#leftGraphStyle').change();
				}
				 else {
					 $('#appGraphData').hide();
				$('#appGridData').show();
				$(".stylec").hide();
		    $('#pieContainer').show();
			$('#barContainer').hide();
			$('#lineContainer').hide();
			 $('#weeklineContainer').hide();
				$('#weekdashContainer').hide();
				$('#monthbarContainer').hide();
				$('#monthdashContainer').hide();
				$('#leftGraphStyle').parent().hide();
				$('#headercontent').show();
				drawMultiGraphsAppGrid("overview");
					 }
		}
}



var weekGraph  = null;
var monthGraph  = null;

function drawMultiGraphsApp(condition)
{
if(condition == 'weekly')
{
	var dataVal = null;
	var urlVal = null;
	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsWeekDateCondition";
		msg = "Application Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsWeek";
		msg = "Moudle Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsWeekForModule";
		msg = "Screen Vs No of hits";
		}

	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		data:dataVal,
		async:false,
		success:function(dataVal){
			weekGraph = dataVal;
		}
	});


}
else if(condition == 'monthly')
{

	var dataVal = null;
	var urlVal = null;
	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsMonthDateCondition";
		msg = "Application Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsMonth";
		msg = "Moudle Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsMonthForModule";
		msg = "Screen Vs No of hits";
		}

	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		data:dataVal,
		async:false,
		success:function(dataVal){
			monthGraph = dataVal;
		}
	});


}
else
	{


	if($('#application').val() == 'select' && $('#module').val() == 'select'  )
	{
		$.ajax({
			url:"/usage/rest/metrics/getOverviewChartWithDate",
			type:"post",
			dataType : 'json',
			data:{startDate:$('#startdate').val(),endDate:$('#enddate').val()},
			success:function(dataVal){
				pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),"Application Vs No of Hits");
			}
		});
	}
	else if($('#application').val() != 'select' && $('#module').val() == 'select'  )
	{
		$.ajax({
			url:"/usage/rest/metrics/getOverviewApplicationChart",
			type:"post",
			dataType : 'json',
			data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
			success:function(dataVal){
				pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),"Module Vs No of Hits");
			}
		});
	}
	else if($('#application').val() != 'select' && $('#module').val() != 'select' )
	{
		$.ajax({
			url:"/usage/rest/metrics/getOverviewModuleChart",
			type:"post",
			dataType : 'json',
			data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
			success:function(dataVal){
				pieChart('#pieContainer',consolidataArrAjax(jQuery.parseJSON(dataVal.applicationSide)),"Screen Vs No of Hits");
			}
		});
	}



	}
}

function drawMultiGraphsAppGrid(condition)
{
if(condition == 'weekly')
{
	var dataVal = null;
	var urlVal = null;
	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsWeekDateCondition";
		msg = "Application Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsWeek";
		msg = "Moudle Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsWeekForModule";
		msg = "Screen Vs No of hits";
		}

	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		data:dataVal,
		async:false,
		success:function(dataVal){
			weekGraph = dataVal;
		}
	});


}
else if(condition == 'monthly')
{

	var dataVal = null;
	var urlVal = null;
	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsMonthDateCondition";
		msg = "Application Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsMonth";
		msg = "Moudle Vs No of hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsHitsVsMonthForModule";
		msg = "Screen Vs No of hits";
		}

	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		data:dataVal,
		async:false,
		success:function(dataVal){
			monthGraph = dataVal;
		}
	});


}
else
	{


	if($('#application').val() == 'select' && $('#module').val() == 'select'  )
	{
		$.ajax({
			url:"/usage/rest/metrics/getOverviewChartWithDate",
			type:"post",
			dataType : 'json',
			data:{startDate:$('#startdate').val(),endDate:$('#enddate').val()},
			success:function(dataVal){
				$('#appGridData').html(drawPieTable("Application","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
			}
		});
	}
	else if($('#application').val() != 'select' && $('#module').val() == 'select'  )
	{
		$.ajax({
			url:"/usage/rest/metrics/getOverviewApplicationChart",
			type:"post",
			dataType : 'json',
			data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
			success:function(dataVal){
				$('#appGridData').html(drawPieTable("Module","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
			}
		});
	}
	else if($('#application').val() != 'select' && $('#module').val() != 'select' )
	{
		$.ajax({
			url:"/usage/rest/metrics/getOverviewModuleChart",
			type:"post",
			dataType : 'json',
			data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
			success:function(dataVal){
				$('#appGridData').html(drawPieTable("Averege Response Time","","Hits",jQuery.parseJSON(dataVal.applicationSide)));
			}
		});
	}



	}
}

var drawServerGraph = null;

function drawMultiGraphServer()
{
	var dataVal = null;
	var urlVal = null;
	if($('#applicationServer').val() == 'select')
		{
		dataVal ={startdate:$('#startdateServer').val(),enddate:$('#enddateServer').val()};
		urlVal = "/usage/rest/metrics/multiGraphWithDate";
		}
	else if($('#applicationServer').val() != 'select')
		{
		dataVal ={startdate:$('#startdateServer').val(),enddate:$('#enddateServer').val(),application:$('#applicationServer').val()};
		urlVal = "/usage/rest/metrics/multiGraph";
		}
	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		async:false,
		data:dataVal,
		success:function(dataVal){
			drawServerGraph = dataVal;
		}
	});
}

var drawScreenGraph = null;

function drawMultiGraphResponseTime()
{
	var dataVal = null;
	var urlVal = null;
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val()};
		urlVal = "/usage/rest/metrics/getAvgResponseTimeMultiGraphWithDate";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()};
		urlVal = "/usage/rest/metrics/getAvgResponseTimeMultiGraph";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()};
		urlVal = "/usage/rest/metrics/getAvgResponseTimeMultiGraphWithModule";
		}

	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		async:false,
		data:dataVal,
		success:function(dataVal){
		drawScreenGraph = dataVal;

		}
	});

}

var drawUserGraph = null;

function drawMultiGraphUser()
{
	var dataVal = null;
	var urlVal = null;
	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsUserWithDate";
		msg = "Application Vs No of User";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsUser";
		msg = "Module Vs No of User";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		dataVal ={startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()};
		urlVal = "/usage/rest/metrics/findApplicationVsUserWithModule";
		msg = "Screen Vs No of User";
		}

	$.ajax({
		url:urlVal,
		type:"post",
		dataType : 'json',
		data:dataVal,
		async:false,
		success:function(dataVal){
			drawUserGraph = dataVal;
		}
	});

}

function userTabChange(ele)
{
	$('#userGridData').hide();
	$('#userGraphData').show();
	if($(ele).val() =="bar"){
	$('#barcontainer1').hide();
	$('#pieContaineruserModule').hide();
	$('#serverbarContainer').show();
	$('#serverlineContainer').hide();
	drawMultiGraphUser();
	barGraph(consolidateGraphValues(drawUserGraph),findMessageUser(),$('#serverbarContainer'));
	}
	else if($(ele).val()=="line"){

		$('#barcontainer1').hide();
		$('#pieContaineruserModule').hide();
	$('#serverbarContainer').hide();
	$('#serverlineContainer').show();
	drawMultiGraphUser();
	lineGraph(consolidateGraphValues(drawUserGraph),findMessageUser(),$('#serverlineContainer'));
		}
	 else if($(ele).val() =="pie"){
				$('#barcontainer1').hide();
			$('#serverbarContainer').hide();
			$('#pieContaineruserModule').show();
			$('#serverlineContainer').hide();

			if($('#application').val() == 'select' && $('#module').val() == 'select'  )
			{
				$.ajax({
					url:"/usage/rest/metrics/getOverviewChartWithDate",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdate').val(),endDate:$('#enddate').val()},
					success:function(dataVal){
						pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Application Vs No of Users");
					}
				});
			}
			else if($('#application').val() != 'select' && $('#module').val() == 'select'  )
			{
				$.ajax({
					url:"/usage/rest/metrics/getOverviewApplicationChart",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
					success:function(dataVal){
						pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Module Vs No of Users");
					}
				});
			}
			else if($('#application').val() != 'select' && $('#module').val() != 'select' )
			{
				$.ajax({
					url:"/usage/rest/metrics/getOverviewModuleChart",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
					success:function(dataVal){
						pieChart('#pieContaineruserModule',consolidataArrAjax(jQuery.parseJSON(dataVal.userSide)),"Screen Vs No of Users");
					}
				});
			}

				}
	 else{

    $('#barcontainer1').show();
	$('#serverbarContainer').hide();
	$('#serverlineContainer').hide();
	$('#pieContaineruserModule').hide();
	drawMultiGraphUser();
	barChart(consolidateGraphValues(drawUserGraph),findMessageUser(),$('#barcontainer1'));
			 }
}

function userTabChangeSelection(ele)
{
if(userGridView == 0)
	{
	userTabChange($(ele));
	}
else
	{
	userTabChangeGrid($(ele));
	}

}


function userTabChangeGrid(ele)
{
	$('#userGridData').show();
	$('#userGraphData').hide();
	if($(ele).val() =="bar"){
	$('#barcontainer1').hide();
	$('#pieContaineruserModule').hide();
	$('#serverbarContainer').show();
	$('#serverlineContainer').hide();
	drawMultiGraphUser();
	$('#userGridData').html(draw3DTable(findMessageUserGrid(), "Item", "User Number", drawUserGraph));
	}
	else if($(ele).val()=="line"){

		$('#barcontainer1').hide();
		$('#pieContaineruserModule').hide();
	$('#serverbarContainer').hide();
	$('#serverlineContainer').show();
	drawMultiGraphUser();
	$('#userGridData').html(draw3DTable(findMessageUserGrid(), "Item", "User Number", drawUserGraph));
		}
	 else if($(ele).val() =="pie"){
				$('#barcontainer1').hide();
			$('#serverbarContainer').hide();
			$('#pieContaineruserModule').show();
			$('#serverlineContainer').hide();

			if($('#application').val() == 'select' && $('#module').val() == 'select'  )
			{
				$.ajax({
					url:"/usage/rest/metrics/getOverviewChartWithDate",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdate').val(),endDate:$('#enddate').val()},
					success:function(dataVal){
						$('#userGridData').html(drawPieTable("Application","","User Number",jQuery.parseJSON(dataVal.userSide)));
					}
				});
			}
			else if($('#application').val() != 'select' && $('#module').val() == 'select'  )
			{
				$.ajax({
					url:"/usage/rest/metrics/getOverviewApplicationChart",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val()},
					success:function(dataVal){
						$('#userGridData').html(drawPieTable("Module","","User Number",jQuery.parseJSON(dataVal.userSide)));
					}
				});
			}
			else if($('#application').val() != 'select' && $('#module').val() != 'select' )
			{
				$.ajax({
					url:"/usage/rest/metrics/getOverviewModuleChart",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
					success:function(dataVal){
						$('#userGridData').html(drawPieTable("Screen","","User Number",jQuery.parseJSON(dataVal.userSide)));
					}
				});
			}

				}
	 else{

    $('#barcontainer1').show();
	$('#serverbarContainer').hide();
	$('#serverlineContainer').hide();
	$('#pieContaineruserModule').hide();
	drawMultiGraphUser();
	$('#userGridData').html(draw3DTable(findMessageUserGrid(), "Item", "User Number", drawUserGraph));
			 }
}

function screenTabChange(ele)
{
	$('#screenGridData').hide();
	$('#screenGraphData').show();
	if($(ele).val() =="bar"){
		drawMultiGraphResponseTime();
		$('#pieContaineruserScreen').hide();
	$('#dashcontainer1Server').hide();
	$('#serverbarContainerServer').show();
	$('#serverlineContainerServer').hide();
	barGraph(consolidateGraphValues(drawScreenGraph),"Averege Response Time Vs Hits",$('#serverbarContainerServer'));
	}
	else if($(ele).val() =="line"){
		drawMultiGraphResponseTime();
		$('#pieContaineruserScreen').hide();
		$('#dashcontainer1Server').hide();
	$('#serverbarContainerServer').hide();
	$('#serverlineContainerServer').show();
	lineGraph(consolidateGraphValues(drawScreenGraph),"Averege Response Time Vs Hits",$('#serverlineContainerServer'));
		}
		 else if($(ele).val() =="pie"){
			 $('#pieContaineruserScreen').show();
				$('#dashcontainer1Server').hide();
			$('#serverbarContainerServer').hide();
			$('#serverlineContainerServer').hide();
			 if($('#application').val() != 'select' && $('#module').val() != 'select' )
				{
					$.ajax({
						url:"/usage/rest/metrics/getOverviewModuleChart",
						type:"post",
						dataType : 'json',
						data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
						success:function(dataVal){
							pieChart('#pieContaineruserScreen',consolidataArrAjax(jQuery.parseJSON(dataVal.responseSide)),"Averege Response Tme Vs No of Hits");
						}
					});
				}

				}
				 else {
					 drawMultiGraphResponseTime();
					 $('#pieContaineruserScreen').hide();
    $('#dashcontainer1Server').show();
	$('#serverbarContainerServer').hide();
	$('#serverlineContainerServer').hide();
	barChart(consolidateGraphValues(drawScreenGraph),"Averege Response Time Vs Hits",$('#dashcontainer1Server'));
			 }
}

function screenTabChangeSelection(ele)
{

if(screenGridView == 0)
	{
	screenTabChange($(ele));
	}
else
	{
	screenTabChangeGrid($(ele));
	}

}

function screenTabChangeGrid(ele)
{
	$('#screenGridData').show();
	$('#screenGraphData').hide();
	if($(ele).val() =="bar"){
		drawMultiGraphResponseTime();
		$('#pieContaineruserScreen').hide();
	$('#dashcontainer1Server').hide();
	$('#serverbarContainerServer').show();
	$('#serverlineContainerServer').hide();
	$('#screenGridData').html(draw3DTable("Screen", "Averege Response Time","Hits", drawScreenGraph));
	}
	else if($(ele).val() =="line"){
		drawMultiGraphResponseTime();
		$('#pieContaineruserScreen').hide();
		$('#dashcontainer1Server').hide();
	$('#serverbarContainerServer').hide();
	$('#serverlineContainerServer').show();
	$('#screenGridData').html(draw3DTable("Screen", "Averege Response Time", "Hits", drawScreenGraph));
		}
		 else if($(ele).val() =="pie"){
			 $('#pieContaineruserScreen').show();
				$('#dashcontainer1Server').hide();
			$('#serverbarContainerServer').hide();
			$('#serverlineContainerServer').hide();
			 if($('#application').val() != 'select' && $('#module').val() != 'select' )
				{
					$.ajax({
						url:"/usage/rest/metrics/getOverviewModuleChart",
						type:"post",
						dataType : 'json',
						data:{startDate:$('#startdate').val(),endDate:$('#enddate').val(),application:$('#application').val(),module:$('#module').val()},
						success:function(dataVal){
							$('#screenGridData').html(drawPieTable("Averege Response Time","","Hits",jQuery.parseJSON(dataVal.responseSide)));
						}
					});
				}

				}
				 else {
					 drawMultiGraphResponseTime();
					 $('#pieContaineruserScreen').hide();
    $('#dashcontainer1Server').show();
	$('#serverbarContainerServer').hide();
	$('#serverlineContainerServer').hide();
	$('#screenGridData').html(draw3DTable("Screen", "Averege Response Time", "Hits", drawScreenGraph));
			 }
}

function  appTimeTabChangeSelection(ele)
{
if (appGridView == 0)
	{
	appTimeTabChange($(ele));
	}
else
	{
	appTimeTabChangeGrid($(ele));
	}
}

function appTimeTabChange(ele)
{
	$('#appGridData').hide();
	$('#appGraphData').show();
	if($('#perfOver').val() == 'weekly')
	{
		$('#monthdashContainer').hide();
		$('#monthbarContainer').hide();
		$('#lineContainer').hide();
	if($(ele).val()=="bar"){

	$('#weekdashContainer').hide();
	$('#barContainer').show();
	$('#weeklineContainer').hide();
	drawMultiGraphsApp("weekly");
	barGraph(consolidateGraphValues(weekGraph),findMessage(),$('#barContainer'));
	}
	else if($(ele).val()=="line"){

		$('#weekdashContainer').hide();
	$('#barContainer').hide();
	$('#weeklineContainer').show();
	drawMultiGraphsApp("weekly");
	lineGraph(consolidateGraphValues(weekGraph),findMessage(),$('#weeklineContainer'));
		}
		 else {

    $('#weekdashContainer').show();
	$('#weeklineContainer').hide();
	$('#barContainer').hide();
	drawMultiGraphsApp("weekly");
	barChart(consolidateGraphValues(weekGraph),findMessage(),$('#weekdashContainer'));
			 }
	}
	else if($('#perfOver').val() == 'monthly')
		{
		$('#weekdashContainer').hide();
		$('#barContainer').hide();
		$('#weeklineContainer').hide();
		if($(ele).val()=="bar"){
			$('#monthdashContainer').hide();
			$('#monthbarContainer').show();
			$('#lineContainer').hide();
			drawMultiGraphsApp("monthly");
			 barGraph(consolidateGraphValues(monthGraph),findMessage(),$('#monthbarContainer'));
			}
			else if($(ele).val()=="line"){

				$('#monthdashContainer').hide();
			$('#monthbarContainer').hide();
			$('#lineContainer').show();
			drawMultiGraphsApp("monthly");
			lineGraph(consolidateGraphValues(monthGraph),findMessage(),$('#lineContainer'));
				}
				 else {

		    $('#monthdashContainer').show();
			$('#lineContainer').hide();
			$('#monthbarContainer').hide();
			drawMultiGraphsApp("monthly");
			 barChart(consolidateGraphValues(monthGraph),findMessage(),$('#monthdashContainer'));
					 }
		}
	else
		{
		$('#weekdashContainer').hide();
		$('#barContainer').hide();
		$('#weeklineContainer').hide();
		$('#monthdashContainer').hide();
		$('#monthbarContainer').hide();
		$('#lineContainer').hide();
		$('#pieContainer').show();
		}


}


function appTimeTabChangeGrid(ele)
{
	$('#appGridData').show();
	$('#appGraphData').hide();
	if($('#perfOver').val() == 'weekly')
	{
		$('#monthdashContainer').hide();
		$('#monthbarContainer').hide();
		$('#lineContainer').hide();
	if($(ele).val()=="bar"){

	$('#weekdashContainer').hide();
	$('#barContainer').show();
	$('#weeklineContainer').hide();
	drawMultiGraphsApp("weekly");
	$('#appGridData').html(draw3DTable("Week", "Application", "Hits", weekGraph));
	}
	else if($(ele).val()=="line"){

		$('#weekdashContainer').hide();
	$('#barContainer').hide();
	$('#weeklineContainer').show();
	drawMultiGraphsApp("weekly");
	$('#appGridData').html(draw3DTable("Week", "Application", "Hits", weekGraph));
		}
		 else {

    $('#weekdashContainer').show();
	$('#weeklineContainer').hide();
	$('#barContainer').hide();
	drawMultiGraphsApp("weekly");
	$('#appGridData').html(draw3DTable("Week", "Application", "Hits", weekGraph));
			 }
	}
	else if($('#perfOver').val() == 'monthly')
		{
		$('#weekdashContainer').hide();
		$('#barContainer').hide();
		$('#weeklineContainer').hide();
		if($(ele).val()=="bar"){
			$('#monthdashContainer').hide();
			$('#monthbarContainer').show();
			$('#lineContainer').hide();
			drawMultiGraphsApp("monthly");
			 $('#appGridData').html(draw3DTable("Month", "Application", "Hits", monthGraph));
			}
			else if($(ele).val()=="line"){

				$('#monthdashContainer').hide();
			$('#monthbarContainer').hide();
			$('#lineContainer').show();
			drawMultiGraphsApp("monthly");
			$('#appGridData').html(draw3DTable("Month", "Application", "Hits", monthGraph));
				}
				 else {

		    $('#monthdashContainer').show();
			$('#lineContainer').hide();
			$('#monthbarContainer').hide();
			drawMultiGraphsApp("monthly");
			 $('#appGridData').html(draw3DTable("Month", "Application", "Hits", monthGraph));
					 }
		}
	else
		{
		$('#weekdashContainer').hide();
		$('#barContainer').hide();
		$('#weeklineContainer').hide();
		$('#monthdashContainer').hide();
		$('#monthbarContainer').hide();
		$('#lineContainer').hide();
		$('#pieContainer').show();
		}


}

function findMessage()
{

	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		msg = "Application Vs No of Hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		msg = "Moudle Vs No of Hits";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		msg = "Screen Vs No of Hits";
		}
	return msg;
}

function findMessageUser()
{

	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		msg = "Application Vs No of User";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		msg = "Moudle Vs No of User";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		msg = "Screen Vs No of User";
		}
	return msg;
}
function findMessageUserGrid()
{

	var msg = "";
	if($('#application').val() == 'select' && $('#module').val() == 'select')
		{
		msg = "Application";
		}
	else if($('#application').val() != 'select' && $('#module').val() == 'select')
		{
		msg = "Moudle";
		}
	else if($('#application').val() != 'select' && $('#module').val() != 'select')
		{
		msg = "Screen";
		}
	return msg;
}

function consolidataArrAjax(dataVal)
{
var result = new Array();
var count = 0;

$.each(dataVal,function(i,val){
		result[count] = new Array();
		result[count][0] = val[0];
		result[count][1] = parseInt(val[1]);
		count++;

});
return result;
}


function consolidateGraphValues(dataVal)
{
	$.each(dataVal[1],function(i,val){
		var dataArr = new Array();
		$.each(val.data,function(j,jval){
			dataArr[j] = parseInt(jval);
		});
		val.data = dataArr;
	});

	return dataVal;
}

function draw3DTable(colHead1, colHead2, colHead3, dataVal)
{
	var result = "";
	var count =0;
	result = "<table width=\"32%\" border=\"0\" cellspacing=\"0\" cellpadding=\"0\""
		+"class=\"dataTable\">"
		+"<thead>"
		+"<tr>"
		+"<th>"+colHead1+"</th>"
		+"<th>"+colHead2+"</th>"
		+"<th>"+colHead3+"</th></tr></thead><tbody>";
	$.each(dataVal[1],function(i,val){
		result += "<tr>";
		count =0;
		$.each(val.data,function(j,jval){
		if(jval != 0 || jval != '0')
			{
			if(dataVal[0].length == (count-1))
				{
				count =0;
				}

			result += "<td>"+dataVal[0][count]+"</td>"+
			  "<td> "+val.name+" </td>"+
			  "<td>"+jval+"</td> </tr>";
			count++;
			}
		});
		result += "</tr>";
	});
	result += "</tbody> </table>";
	return result;
}

function drawPieTable(colHead1, colHead2, colHead3, dataVal)
{

	var result = "";
	result = "<table width=\"32%\" border=\"0\" cellspacing=\"0\" cellpadding=\"0\""
		+"class=\"dataTable\">"
		+"<thead>"
		+"<tr>"
		+"<th>"+colHead1+"</th>"
		+"<th>"+colHead2+"</th>"
		+"<th>"+colHead3+"</th></tr></thead><tbody>";

	$.each(dataVal,function(i,val){
		result += "<tr>"+
				  "<td>"+val[0].split('(')[0]+"</td>"+
				  "<td> </td>"+
				  "<td>"+val[1]+"</td> </tr>";
});

	result += "</tbody> </table>";
	return result;

}


function submitMetricsServer()
{
	setDefaultServerGrid();
	if($('#applicationServer').val() == 'select'  )
	{
		 $('#rightGraphStyleServer').change();
//		teamTasksCustomServer("default","","","");
	}
	else if($('#applicationServer').val() != 'select' )
	{
		 $('#rightGraphStyleServer').change();
//		teamTasksCustomServer("application",$('#applicationServer').val(),"","");
	}
	else
		{
		}
}


function teamTasksCustomServer(category,id,id1,id2)
{
	if(category == "application")
	{
	if(serverGridView == 0)
	{
	processDateAllModVsServerOnly("Server Vs No Of Hits",id);
	}
	else
		{
		processDateAllModVsServerOnlyGrid("Server Vs No Of Hits",id);
		}
	}
	else if(category == "default")
	{
		if(serverGridView == 0)
		{
				processDateAllAppVsServerOnlyCondition("Server Vs No Of Hits",id);
		}
		else
		{
			processDateAllAppVsServerOnlyConditionGrid("Server Vs No Of Hits",id);
		}
	}

}

function processDateAllModVsServerOnly(msg,id)
{
	$.ajax({
		url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewAllCondition",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val(),application:$('#applicationServer').val()},
		success:function(dataVal){

			pieChart('#pieContaineruserModuleServerRight',consolidataArrAjax(dataVal),msg);
		}
	});
}

function processDateAllModVsServerOnlyGrid(msg,id)
{
	$.ajax({
		url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewAllCondition",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val(),application:$('#applicationServer').val()},
		success:function(dataVal){

			$('#serverGridData').html(drawPieTable("Server","","Hits",dataVal));
		}
	});
}


function processDateAllAppVsServerOnlyCondition(msg)
{
	$.ajax({
		url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewDateCondition",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val()},
		success:function(dataVal){
			pieChart('#pieContaineruserModuleServerRight',consolidataArrAjax(dataVal),msg);
		}
	});
}

function processDateAllAppVsServerOnlyConditionGrid(msg)
{
	$.ajax({
		url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewDateCondition",
		type:"post",
		dataType : 'json',
		data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val()},
		success:function(dataVal){
			$('#serverGridData').html(drawPieTable("Server","","Hits",dataVal));
		}
	});
}

function severGraphStyle(ele)
{
	$('#serverGridData').hide();
	$('#serverGraphData').show();
	if($(ele).val()=="bar"){
	$('#barcontainer1ServerRight').hide();
	$('#pieContaineruserModuleServerRight').hide();
	$('#serverbarContainerServerRight').show();
	$('#serverlineContainerServerRight').hide();
	drawMultiGraphServer();
	barGraph(consolidateGraphValues(drawServerGraph),"Server Vs No of Hits",$('#serverbarContainerServerRight'));
	}
	else if($(ele).val()=="line"){
		$('#barcontainer1ServerRight').hide();
		$('#pieContaineruserModuleServerRight').hide();
	$('#serverbarContainerServerRight').hide();
	$('#serverlineContainerServerRight').show();
	drawMultiGraphServer();
	lineGraph(consolidateGraphValues(drawServerGraph),"Server Vs No of Hits",$('#serverlineContainerServerRight'));
		}
	 else if($(ele).val()=="pie"){
			$('#barcontainer1ServerRight').hide();
			$('#serverbarContainerServerRight').hide();
			$('#pieContaineruserModuleServerRight').show();
			$('#serverlineContainerServerRight').hide();
			if($("#applicationServer option").length > 0 && $('#applicationServer').val() == 'select' )
			{
				$.ajax({
					url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewDateCondition",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val()},
					success:function(dataVal){
						pieChart('#pieContaineruserModuleServerRight',consolidataArrAjax(dataVal),"Server Vs No of Hits");
					}
				});
			}
			else if($("#applicationServer option").length > 0 && $('#applicationServer').val() != 'select' )
			{
				$.ajax({
					url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewAllCondition",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val(),application:$('#applicationServer').val()},
					success:function(dataVal){

						pieChart('#pieContaineruserModuleServerRight',consolidataArrAjax(dataVal),"Server Vs No of Hits");
					}
				});
			}
		}
	 else{
    $('#barcontainer1ServerRight').show();
	$('#serverbarContainerServerRight').hide();
	$('#serverlineContainerServerRight').hide();
	$('#pieContaineruserModuleServerRight').hide();
	 drawMultiGraphServer();
		barChart(consolidateGraphValues(drawServerGraph),"Server Vs No of Hits",$('#barcontainer1ServerRight'));
			 }
}

function severGraphStyleSelection(ele)
{
if(serverGridView  == 0)
	{
	severGraphStyle($(ele));
	}
else
	{
	severGraphStyleGrid($(ele));
	}
}



function severGraphStyleGrid(ele)
{
	$('#serverGridData').show();
	$('#serverGraphData').hide();
	if($(ele).val()=="bar"){
	$('#barcontainer1ServerRight').hide();
	$('#pieContaineruserModuleServerRight').hide();
	$('#serverbarContainerServerRight').show();
	$('#serverlineContainerServerRight').hide();
	drawMultiGraphServer();
	 $('#serverGridData').html(draw3DTable("Server", "Items", "Hits", drawServerGraph));
	}
	else if($(ele).val()=="line"){
		$('#barcontainer1ServerRight').hide();
		$('#pieContaineruserModuleServerRight').hide();
	$('#serverbarContainerServerRight').hide();
	$('#serverlineContainerServerRight').show();
	drawMultiGraphServer();
	 $('#serverGridData').html(draw3DTable("Server", "Items",  "Hits", drawServerGraph));
		}
	 else if($(ele).val()=="pie"){
			$('#barcontainer1ServerRight').hide();
			$('#serverbarContainerServerRight').hide();
			$('#pieContaineruserModuleServerRight').show();
			$('#serverlineContainerServerRight').hide();
			if($('#applicationServer').val() == 'select'  )
			{
				$.ajax({
					url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewDateCondition",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val()},
					success:function(dataVal){
						$('#serverGridData').html(drawPieTable("Server","","Hits",dataVal));
					}
				});
			}
			else if($('#applicationServer').val() != 'select' )
			{
				$.ajax({
					url:"/usage/rest/metrics/findServerVsNoOfHitsForOverviewAllCondition",
					type:"post",
					dataType : 'json',
					data:{startDate:$('#startdateServer').val(),endDate:$('#enddateServer').val(),application:$('#applicationServer').val()},
					success:function(dataVal){

						$('#serverGridData').html(drawPieTable("Server","","Hits",dataVal));
					}
				});
			}
		}
	 else{
    $('#barcontainer1ServerRight').show();
	$('#serverbarContainerServerRight').hide();
	$('#serverlineContainerServerRight').hide();
	$('#pieContaineruserModuleServerRight').hide();
	 drawMultiGraphServer();
		 $('#serverGridData').html(draw3DTable("Server", "Items",  "Hits", drawServerGraph));
			 }
}


function setDefaultServerGrid()
{
	$('#barcontainer1ServerRight').hide();
	$('#serverbarContainerServerRight').hide();
	$('#pieContaineruserModuleServerRight').show();
	$('#serverlineContainerServerRight').hide();
	$('#rightGraphStyleServer').val('pie');
	$('#rightGraphStyleServer').change();
}

function setDefaultGrid()
{
	$('#pieContaineruserScreen').show();
	$('#dashcontainer1Server').hide();
	$('#serverbarContainerServer').hide();
	$('#serverlineContainerServer').hide();
	$('#weekdashContainer').hide();
	$('#barContainer').hide();
	$('#weeklineContainer').hide();
	$('#monthdashContainer').hide();
	$('#monthbarContainer').hide();
	$('#lineContainer').hide();
	$('#pieContaineruserScreen').show();
	$('#dashcontainer1Server').hide();
	$('#serverbarContainerServer').hide();
	$('#serverlineContainerServer').hide();
	$('#barcontainer1').hide();
	$('#serverbarContainer').hide();
	$('#pieContaineruserModule').show();
	$('#serverlineContainer').hide();
	$('#leftGraphStyle').parent().hide();
/*	$('#perfOver').val('overview');
	$('#perfOver').change();
	$('#rightGraphStyle').val('pie');
	$('#rightGraphStyle').change();
	$('#botGraphStyle').val('pie');
	$('#botGraphStyle').change();*/
	$('.headercontent').show();
}
