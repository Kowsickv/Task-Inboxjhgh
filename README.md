<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="ISO-8859-1">
		<title>Login</title>
		
		<!-- jQuery JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
		
		<!-- Angular JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
		
		<!-- Angular JS ngRoute File -->
		<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.15/angular-route.js"></script>
		
		<!-- Bootstrap CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css">
		
		<!-- Bootstrap Theme - CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap-theme.min.css">
		
		<!-- Bootstrap JS File -->
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
		
		<!-- My angular JS File -->		
		<script type="text/javascript" src="myApp.js"></script>
		
	</head>
	<body ng-controller="MainCtrl">
		<div class="container" style="width: 300px;margin-top: 50px">
			<form class="form-signin" ng-submit="enter()">
				<h2 class="form-signin-heading">Sign in</h2>
				<label for="userName" class="sr-only">User Name</label>
				<input type="text" id="user" class="form-control" placeholder="User" ng-model="role" required autofocus><br>
				<button class="btn btn-md btn-primary btn-block" type="submit">Sign in</button>
			</form>
		</div>
	</body>
</html>




















var app = angular.module('myApp', []);

app.service('roles',function(){
	return {
		"approver" : ['Approved','Rejected','Delegated'],
		"performer":['Completed','InComplete'],
		"user":[]		
	};
});



app.controller('MainCtrl', function($scope,$window,$rootScope) {
	
	$scope.create=false;
	/*					This is for Redirection					*/
	var pageMap = {
			"user":"user.html",
			"approver":"approver.html",
			"performer":"performer.html"
	};
	
	$scope.enter = function(){		
		var refHtml = pageMap[$scope.role];
		$scope.changeRoute(refHtml);
	};
	
	$scope.changeRoute = function(url, forceReload) {

		$scope = $scope || angular.element(document).scope();

		if(forceReload) { 
			$window.location.href = url;
		} else {	
			$window.location.href = url;
		}
	};
});
	/*				This is for End of Redirection				*/
	
	
	
	/*				This is adding Form Data					*/
	
app.controller('userctrl', function($scope,$window,$rootScope) {
	$scope.addName = function(role){
	
		
	/*				This is Task ID Generator					*/
		
		
        var lastVal =  JSON.parse(localStorage.getItem("lastValue"));
        if (!lastVal) {
            $rootScope.lastVal = 1;
            var lastVal = $rootScope.lastVal;
            localStorage.setItem("lastValue", JSON.stringify(lastVal));
        } else {
            $rootScope.lastVal = lastVal + 1;
            var lastVal = $rootScope.lastVal;
            localStorage.setItem("lastValue", JSON.stringify(lastVal));
        }
        $scope.task_id = $rootScope.lastVal;
     /*				End of Task ID Generator					*/
		
		
		var userFormData = [];
		
		var task = {
				"task_id":$scope.task_id,
				"user_id":$scope.user_id,
				"user_name":$scope.user_name,
				"manager_name":$scope.manager_name,
				"department":$scope.department,
				"machine_name":$scope.machine_name,
				"location":$scope.location,
				"taskDescription":$scope.taskDescription,
				"requested_date":$scope.requested_date,
				"software":$scope.software,
				"version":$scope.version,
				"license":$scope.license,
				"status":"Pending",
				'assignedTo':'approver',
				'actions':[]
		};
		
		var taskListObject = [] ;
		taskListObject = JSON.parse(localStorage.getItem("taskList"));
		
		if(taskListObject == undefined){
			userFormData[0] = task;			
		} 
		else {
			for(var i = 0; i < taskListObject.length ; i ++)
				userFormData[i] = taskListObject[i];
			userFormData[taskListObject.length] = task;
		}
		localStorage.setItem("taskList", JSON.stringify(userFormData));
		$scope.taskList = userFormData;
	};
	$scope.taskList = JSON.parse(localStorage.getItem("taskList"));

	/*				This is End of adding Form Data					*/
	

});





app.controller('Approverctrl',function($scope,$http,roles,$rootScope){
	
/*			This is Modyfing Task List for Approver				*/
	var taskList = JSON.parse(localStorage.getItem("taskList"));
	var taskListForApprover = [];
	
	for(var i = 0; i < taskList.length; i ++){		
		taskList[i].actions = roles[taskList[i].assignedTo];
		taskListForApprover[i] = taskList[i];
	}

	$scope.taskListForApprover = taskListForApprover;
	
/*		This is End of Modyfing Task List for Approver			*/
	
	
/*				This is Selecting Status With ID				*/
	var statusList = [];
	var ListScope = [];
	$scope.change = function(action){
		if(action=="Approved"||"Rejected"||"Delegated"){
			var table = document.getElementsByTagName("table")[0];
			var tbody = table.getElementsByTagName("tbody")[0];
			tbody.onclick = function (e) {
			    e = e || window.event;
			    var data = [];
			    var target = e.srcElement || e.target;
			    while (target && target.nodeName !== "TR") {
			        target = target.parentNode;
			    }
			    if (target) {
			        var cells = target.getElementsByTagName("td")
			        data.push(cells[0].innerHTML);	        
			    }
			    statusList[data]=action;
			    console.log(statusList);
			    localStorage.setItem("statusList", JSON.stringify(statusList));
			    
			};
		}
	};
/*				This is Storing Status With Task List				*/
	var j=0;
	var ApproverFullList;
	var check;
	$scope.saveChanges = function(){
		
	for(i=0;i<statusList.length;i++){
		j = i+1;
		if(statusList[j]==undefined){

		}
		else{
			$scope.taskListForApprover[i].status = statusList[j];
			$scope.taskListForApprover[i].assignedTo ="performer";
			ApproverFullList = $scope.taskListForApprover
			localStorage.setItem("approverList", JSON.stringify(ApproverFullList));
			localStorage.setItem("taskList", JSON.stringify(ApproverFullList));
		}
		}
	
	}
	
	var approverFullLists = JSON.parse(localStorage.getItem("taskList"));
	$scope.display = approverFullLists;
	
/*				End of Storing Status With Task List				*/	
	
/*				End of Selecting Status With ID			*/
});




app.controller('Performerctrl',function($scope,$http,roles,$rootScope){
	
	/*			This is Modyfing Task List for Approver				*/
		var taskList = JSON.parse(localStorage.getItem("taskList"));
		var taskListForApprover = [];
		
		for(var i = 0; i < taskList.length; i ++){		
			taskList[i].actions = roles[taskList[i].assignedTo];
			taskListForApprover[i] = taskList[i];
		}
		
		$scope.taskListForApprover = taskListForApprover;
	/*		This is End of Modyfing Task List for Approver			*/
		
		
	/*				This is Selecting Status With ID				*/
		var statusList = [];
		var ListScope = [];
		$scope.change = function(action){
			if(action=="Completed"||"InComplete"){
				var table = document.getElementsByTagName("table")[0];
				var tbody = table.getElementsByTagName("tbody")[0];
				tbody.onclick = function (e) {
				    e = e || window.event;
				    var data = [];
				    var target = e.srcElement || e.target;
				    while (target && target.nodeName !== "TR") {
				        target = target.parentNode;
				    }
				    if (target) {
				        var cells = target.getElementsByTagName("td")
				        data.push(cells[0].innerHTML);	        
				    }
				    statusList[data]=action;
				    console.log(statusList);
				    localStorage.setItem("statusList", JSON.stringify(statusList));
				    
				};
			}
		};
	/*				This is Storing Status With Task List				*/
		var j=0;
		var ApproverFullList;
		var check;
		$scope.saveChanges = function(){
			
		for(i=0;i<statusList.length;i++){
			j = i+1;
			if(statusList[j]==undefined){

			}
			else{
				$scope.taskListForApprover[i].status = statusList[j];
				ApproverFullList = $scope.taskListForApprover;
				localStorage.setItem("approverList", JSON.stringify(ApproverFullList));
				localStorage.setItem("taskList", JSON.stringify(ApproverFullList));
			}
			}
		
		}
		
		var approverFullLists = JSON.parse(localStorage.getItem("taskList"));
		$scope.display = approverFullLists;
		
	/*				End of Storing Status With Task List				*/	
		
	/*				End of Selecting Status With ID			*/
	});









<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="ISO-8859-1">
		<title>User</title>
		
		<!-- jQuery JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
		
		<!-- Angular JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
		
		<!-- Angular JS ngRoute File -->
		<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.15/angular-route.js"></script>
		
		<!-- Bootstrap CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css">
		
		<!-- Bootstrap Theme - CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap-theme.min.css">
		
		<!-- Bootstrap JS File -->
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
		
		<!-- My angular JS File -->		
		<script type="text/javascript" src="myApp.js"></script>
		
		<!-- <script type="text/javascript" src="DatePicker.js"></script> -->		
		
		<style type="text/css">

		</style>
	</head>
	<body style="background-color: #dddbdb;" ng-controller='userctrl'>
		<div style="margin-top: 15px">
			<h3 style="font-weight: bold; color: #9F9273;text-align: center;">Task List</h3>
  			<div style="background-color: white;width:1230px;margin-left: 100px;">

					<table class="table table-hover" style="width:1190px;border: 1px;margin-left: 20px;">
						<thead style="font-weight: bold; color: #9F9273">
							<tr>
								<td>Task ID</td>
								<td>Requested By</td>
								<td>Department</td>
								<td>Approver</td>
								<td>Location</td>
								<td>Description</td>
								<td>Requested Date</td>
								<td>Software</td>
								<td>Version</td>
								<td>License</td>
								<td>Status</td>
							</tr>
						</thead>
						
						<tbody>
							<tr ng-repeat="task in taskList track by $index">
				        		<td>{{task.task_id}}</td>
				        		<td>{{task.user_name}}</td>
				        		<td>{{task.department}}</td>
				        		<td>{{task.manager_name}}</td>
				        		<td>{{task.location}}</td>
				        		<td>{{task.taskDescription}}</td>
				        		<td>{{task.requested_date}}</td>
				        		<td>{{task.software}}</td>
				        		<td>{{task.version}}</td>
				        		<td>{{task.license}}</td>
				        		<td>{{task.status}}</td>
			      			</tr>
			      			
		      			</tbody>
		    		</table>
			</div>
			<div align="center">
				<button type="button" class="btn btn-default"  ng-click="create = ! create">Create Task</button>
			</div>
			 <div style="background-color: white;width: 500px;margin-left: 25px;" ng-show="create">
				<form role="form" ng-submit="addName('approver')">
					<div class="form-group">
						<label>User id:</label>
						<input type="text" ng-model="user_id" class="form-control"  placeholder="User ID" />
					</div>
					<div class="form-group">
						<label>User Name</label>
						<input type="text" ng-model="user_name" class="form-control"  placeholder="User Name" />
					</div>
					<div class="form-group">
						<label>Manager Name</label>
						<input type="text" ng-model="manager_name" class="form-control"  placeholder="Manager Name" />
					</div>
					<div class="form-group">
						<label>Department</label>
						<input type="text" ng-model="department" class="form-control"  placeholder="Department" />
					</div>
					<div class="form-group">
						<label>Machine Name</label>
						<input type="text" ng-model="machine_name" class="form-control"  placeholder="Machine Name" />
					</div>
					<div class="form-group">
						<label>Location</label>
						<input type="text" ng-model="location" class="form-control"  placeholder="Location" />
					</div>
					<div class="form-group">
						<label>Task Description</label>
						<input type="text" ng-model="taskDescription" class="form-control"  placeholder="Task Description" />
					</div>
					<div class="form-group">
						<label>Requested Date</label>
						<input type="text" ng-model="requested_date" class="form-control"  placeholder="Requested Date" />
					</div>
					<div class="form-group">
						<label>Software</label>
						<input type="text" ng-model="software" class="form-control"  placeholder="Software" />
					</div>
					<div class="form-group">
						<label>Version</label>
						<input type="text" ng-model="version" class="form-control"  placeholder="Version" />
					</div>
					<div class="form-group">
						<label>License</label>
						<input type="text" ng-model="license" class="form-control"  placeholder="License" />
					</div>
					<div align="center">				
						<button type="submit" class="btn btn-default" ng-click="create = ! create">Create</button>
					</div>
				</form>
			</div>
		</div>
</body>
</html>








<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="ISO-8859-1">
		<title>Approver</title>
		
		<!-- jQuery JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
		
		<!-- Angular JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
		
		<!-- Angular JS ngRoute File -->
		<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.15/angular-route.js"></script>
		
		<!-- Bootstrap CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css">
		
		<!-- Bootstrap Theme - CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap-theme.min.css">
		
		<!-- Bootstrap JS File -->
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
		
		<!-- My angular JS File -->		
		<script type="text/javascript" src="myApp.js"></script>
		
		<!-- <script type="text/javascript" src="DatePicker.js"></script> -->		
		
		<style type="text/css">

		</style>
	</head>
	<body style="background-color: #dddbdb;" ng-controller='Approverctrl'>
		<div style="margin-top: 15px;">
			<h3 style="font-weight: bold; color: #9F9273;text-align: center;">Task List</h3>
  			<div style="background-color: white;width:1230px;margin-left: 100px;">

					<table class="table table-hover" style="width:1190px;border: 1px;margin-left: 20px;">
						<thead style="font-weight: bold; color: #9F9273">
							<tr>
								<td>Task ID</td>
								<td>Requested By</td>
								<td>Department</td>
								<td>Approver</td>
								<td>Location</td>
								<td>Description</td>
								<td>Requested Date</td>
								<td>Software</td>
								<td>Version</td>
								<td>License</td>
								<td>Status</td>
								<td>Action</td>
							</tr>
						</thead>
						
						<tbody>
							<tr ng-repeat="task in taskListForApprover | filter:{status:'Pending'}">
				        		<td>{{task.task_id}}</td>
				        		<td>{{task.user_name}}</td>
				        		<td>{{task.department}}</td>
				        		<td>{{task.manager_name}}</td>
				        		<td>{{task.location}}</td>
				        		<td>{{task.taskDescription}}</td>
				        		<td>{{task.requested_date}}</td>
				        		<td>{{task.software}}</td>
				        		<td>{{task.version}}</td>
				        		<td>{{task.license}}</td>
				        		<td>{{task.status}}</td>
				        		<td>
				        			<select class="form-control input-sm selectpicker" ng-options="stat as stat for stat in task.actions" ng-model="action" ng-change="change(action)"></select>
				        		</td>
				        		
			      			</tr>
		      			</tbody>
		    		</table>
			</div>
			<div align="center">
				<button type="button" ng-click="saveChanges()" class="btn btn-default">Save Changes</button>
			</div>
<!-- 			 <div style="background-color: white;width: 500px;margin-left: 25px;" ng-show="create">
				<form role="form" ng-submit="addName()">
					<div class="form-group">
						<label>User id:</label>
						<input type="text" ng-model="user_id" class="form-control"  placeholder="User ID" />
					</div>
					<div class="form-group">
						<label>User Name</label>
						<input type="text" ng-model="user_name" class="form-control"  placeholder="User Name" />
					</div>
					<div class="form-group">
						<label>Manager Name</label>
						<input type="text" ng-model="manager_name" class="form-control"  placeholder="Manager Name" />
					</div>
					<div class="form-group">
						<label>Department</label>
						<input type="text" ng-model="department" class="form-control"  placeholder="Department" />
					</div>
					<div class="form-group">
						<label>Machine Name</label>
						<input type="text" ng-model="machine_name" class="form-control"  placeholder="Machine Name" />
					</div>
					<div class="form-group">
						<label>Location</label>
						<input type="text" ng-model="location" class="form-control"  placeholder="Location" />
					</div>
					<div class="form-group">
						<label>Task Description</label>
						<input type="text" ng-model="taskDescription" class="form-control"  placeholder="Task Description" />
					</div>
					<div class="form-group">
						<label>Requested Date</label>
						<input type="text" ng-model="requested_date" class="form-control"  placeholder="Requested Date" />
					</div>
					<div class="form-group">
						<label>Software</label>
						<input type="text" ng-model="software" class="form-control"  placeholder="Software" />
					</div>
					<div class="form-group">
						<label>Version</label>
						<input type="text" ng-model="version" class="form-control"  placeholder="Version" />
					</div>
					<div class="form-group">
						<label>License</label>
						<input type="text" ng-model="license" class="form-control"  placeholder="License" />
					</div>
					<div align="center">
						<button type="submit" class="btn btn-default">Create</button>
					</div>
				</form>
			</div> -->
		</div>
	</body>
</html>




<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="ISO-8859-1">
		<title>Performer</title>
		
		<!-- jQuery JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
		
		<!-- Angular JS File -->
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
		
		<!-- Angular JS ngRoute File -->
		<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.15/angular-route.js"></script>
		
		<!-- Bootstrap CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap.min.css">
		
		<!-- Bootstrap Theme - CSS File -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/css/bootstrap-theme.min.css">
		
		<!-- Bootstrap JS File -->
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
		
		<!-- My angular JS File -->		
		<script type="text/javascript" src="myApp.js"></script>
		
		<!-- <script type="text/javascript" src="DatePicker.js"></script> -->		
		
		<style type="text/css">

		</style>
	</head>
	<body style="background-color: #dddbdb;" ng-controller='Performerctrl'>
		<div style="margin-top: 15px;">
			<h3 style="font-weight: bold; color: #9F9273;text-align: center;">Task List</h3>
  			<div style="background-color: white;width:1230px;margin-left: 100px;">

					<table class="table table-hover" style="width:1190px;border: 1px;margin-left: 20px;">
						<thead style="font-weight: bold; color: #9F9273">
							<tr>
								<td>Task ID</td>
								<td>Requested By</td>
								<td>Department</td>
								<td>Approver</td>
								<td>Location</td>
								<td>Description</td>
								<td>Requested Date</td>
								<td>Software</td>
								<td>Version</td>
								<td>License</td>
								<td>Status</td>
								<td>Action</td>
							</tr>
						</thead>
						
						<tbody>
							<tr ng-repeat="task in taskListForApprover | filter:{status:'Approved'}">
				        		<td>{{task.task_id}}</td>
				        		<td>{{task.user_name}}</td>
				        		<td>{{task.department}}</td>
				        		<td>{{task.manager_name}}</td>
				        		<td>{{task.location}}</td>
				        		<td>{{task.taskDescription}}</td>
				        		<td>{{task.requested_date}}</td>
				        		<td>{{task.software}}</td>
				        		<td>{{task.version}}</td>
				        		<td>{{task.license}}</td>
				        		<td>{{task.status}}</td>
				        		<td>
				        			<select class="form-control input-sm selectpicker" ng-options="stat as stat for stat in task.actions" ng-model="action" ng-change="change(action)"></select>
				        		</td>
				        		
			      			</tr>
		      			</tbody>
		    		</table>
			</div>
			<div align="center">
				<button type="button" ng-click="saveChanges()" class="btn btn-default">Save Changes</button>
			</div>
<!-- 			 <div style="background-color: white;width: 500px;margin-left: 25px;" ng-show="create">
				<form role="form" ng-submit="addName()">
					<div class="form-group">
						<label>User id:</label>
						<input type="text" ng-model="user_id" class="form-control"  placeholder="User ID" />
					</div>
					<div class="form-group">
						<label>User Name</label>
						<input type="text" ng-model="user_name" class="form-control"  placeholder="User Name" />
					</div>
					<div class="form-group">
						<label>Manager Name</label>
						<input type="text" ng-model="manager_name" class="form-control"  placeholder="Manager Name" />
					</div>
					<div class="form-group">
						<label>Department</label>
						<input type="text" ng-model="department" class="form-control"  placeholder="Department" />
					</div>
					<div class="form-group">
						<label>Machine Name</label>
						<input type="text" ng-model="machine_name" class="form-control"  placeholder="Machine Name" />
					</div>
					<div class="form-group">
						<label>Location</label>
						<input type="text" ng-model="location" class="form-control"  placeholder="Location" />
					</div>
					<div class="form-group">
						<label>Task Description</label>
						<input type="text" ng-model="taskDescription" class="form-control"  placeholder="Task Description" />
					</div>
					<div class="form-group">
						<label>Requested Date</label>
						<input type="text" ng-model="requested_date" class="form-control"  placeholder="Requested Date" />
					</div>
					<div class="form-group">
						<label>Software</label>
						<input type="text" ng-model="software" class="form-control"  placeholder="Software" />
					</div>
					<div class="form-group">
						<label>Version</label>
						<input type="text" ng-model="version" class="form-control"  placeholder="Version" />
					</div>
					<div class="form-group">
						<label>License</label>
						<input type="text" ng-model="license" class="form-control"  placeholder="License" />
					</div>
					<div align="center">
						<button type="submit" class="btn btn-default">Create</button>
					</div>
				</form>
			</div> -->
		</div>
	</body>
</html>
