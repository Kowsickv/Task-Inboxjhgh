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

