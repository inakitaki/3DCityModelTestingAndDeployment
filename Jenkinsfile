pipeline {
    agent any 
	
	environment { 
        srs = '25830'
		commitid = 'citymodeltestinganddeployment'
		body = ''
		citygml = ''	
    }

    stages {
        stage('COMMIT STAGE') { 
			environment { 
                validateXML = ''
				validateXSD = ''
				correctGeometryFirstLast = ''
				validateCodeType = ''
				citygmlStadistics = ''
            }
            steps { 
				script {
					git url: 'https://github.com/inakitaki/3DCityModelTestingAndDeployment.git'
					citygml = readFile('citymodeltestinganddeployment.xml')
					body = cambiarCaracteresRaros(citygml)
					body = "param1=" + body
					
					//Syntactic validation - Validate XML - TEST
					echo 'Syntactic validation - Validate XML - TEST'
					validateXML = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CityGMLValidateXML',body)
					
					//Syntactic validation - Validate XSD - TEST
					echo 'Syntactic validation - Validate XSD - TEST'
					validateXSD = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CityGMLValidateXSD',body)
					
					//Syntactic validation - Correct Geometry - First and Last Equal - TEST
					echo 'Syntactic validation - Correct Geometry - First and Last Equal - TEST'
					correctGeometryFirstLast = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CorrectFirstAndLastGeometry',body)
					
					//Syntactic validation - Validate Codetype - TEST
					echo 'Syntactic validation - Validate Codetype - TEST'
					body = cambiarCaracteresRaros(correctGeometryFirstLast)
					body = "param1=" + body
					validateCodeType = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/ValidateCodetype',body)
					
					//Code Metrics - Statistics  - TEST
					echo 'Code Metrics - Statistics  - TEST'
					citygmlStadistics = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CityGMLStatistics',body)
				}
            }
        }
		stage('EXTENSION STAGE'){
			environment { 
				citygmlArea = ''
				citygmlOrientation = ''
				citygmlGlobalAdjoining = ''
				citygmlLocalAdjoining = ''
				citygmlCorrectArea = ''
			}
            steps {
				script {				
					//Geometric and Semantic Process - Calculate Areas - TEST
					echo 'Geometric and Semantic Process - Calculate Areas - TEST'
					citygmlArea = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CalculateAreas',body)
					
					//Geometric and Semantic Process - Calculate Orientation - TEST
					echo 'Geometric and Semantic Process - Calculate Orientation - TEST'
					body = cambiarCaracteresRaros(citygmlArea)
					body = "param1=" + body
					citygmlOrientation = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CalculateOrientation',body)
					
					//Geometric and Semantic Process - Calculate Global Adjoining - TEST
					echo 'Geometric and Semantic Process - Calculate Global Adjoining - TEST'
					body = cambiarCaracteresRaros(citygmlOrientation)
					body = "param1=" + body
					citygmlGlobalAdjoining = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CalculateGlobalAdjoining',body)
					
					//Geometric and Semantic Process - Calculate Local Adjoining - TEST
					echo 'Geometric and Semantic Process - Calculate Local Adjoining - TEST'
					body = cambiarCaracteresRaros(citygmlGlobalAdjoining)
					body = "param1=" + body
					citygmlLocalAdjoining = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CalculateLocalAdjoining',body)
					
					//Geometric and Semantic Process - Calculate Correct Area - TEST
					echo 'Geometric and Semantic Process - Calculate Correct Area - TEST'
					body = cambiarCaracteresRaros(citygmlLocalAdjoining)
					body = "param1=" + body
					citygmlCorrectArea = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CalculateCorrectArea',body)
				}
            }
        }
		stage('ACCEPTANCE STAGE - TEST'){
			environment { 
				createDB = ''
				importCityGML = ''
				deployWFS = ''
				testWFS = ''
			}
            steps {
				script {
					//DEPLOY BINARIES - Create DB - TEST
					echo 'DEPLOY BINARIES - Create DB - TEST'
					body = cambiarCaracteresRaros(citygml)
					body = "param1=" + commitid + "&param2=" + srs + "&param3=true"
					createDB = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CreateDB',body)
					
					//DEPLOY BINARIES - Import CityGML - TEST
					echo 'DEPLOY BINARIES - Import CityGML - TEST'
					body = cambiarCaracteresRaros(citygml)
					body = "param1=" + body + "&param2=" + commitid + "&param3=true"
					importCityGML = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/ImportCityGMLToDB',body)
					
					//DEPLOY BINARIES - Deploy WFS - TEST
					echo 'DEPLOY BINARIES - Deploy WFS - TEST'
					body = "param1=" + commitid + "&param2=" + srs + "&param3=true"
					deployWFS = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/DeployWFS',body)
					
					//DEPLOY BINARIES - WFS Test - TEST
					echo 'DEPLOY BINARIES - WFS Test - TEST'
					body = "param1=" + commitid + "&param2=true"
					testWFS = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/WFSTest',body)
				}
            }
        }
		stage('UAT STAGE'){
			environment { 
				createKML = ''
				userInput = ''
			}
            steps {
				script {
					//DEPLOY BINARIES - Create KML - TEST
					echo 'DEPLOY BINARIES - Create KML - TEST'
					body = cambiarCaracteresRaros(citygml)
					body = "param1=" + body + "&param2=" + commitid + "&param3=" + srs + "&param4=UATResultKML"
					createKML = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CreateKMLString',body)
					
					//CESIUM - TEST
					echo 'CESIUM - TEST'
					userInput = input(
					 id: 'userInput', message: 'Is Model Valid?', parameters: [
					 [$class: 'TextParameterDefinition', defaultValue: 'false', description: '3D Model Valid? true/false https://goo.gl/N0GoU5', name: 'cesium'],
					]) 
					echo ("Model Valid: "+userInput)
					comprobarsiErrorUAT(userInput)
				}
            }
        }
		stage('PRODUCTION STAGE'){
			environment { 
				saveCityGMLFinal = ''
			}
            steps {
				script {

					//DEPLOY BINARIES - Create DB - PRODUCTION
					echo 'DEPLOY BINARIES - Create DB - PRODUCTION'
					body = cambiarCaracteresRaros(citygml)
					body = "param1=" + commitid + "&param2=" + srs + "&param3=false"
					createDB = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/CreateDB',body)
					
					//DEPLOY BINARIES - Import CityGML - PRODUCTION
					echo 'DEPLOY BINARIES - Import CityGML - PRODUCTION'
					body = cambiarCaracteresRaros(citygml)
					body = "param1=" + body + "&param2=" + commitid + "&param3=false"
					importCityGML = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/ImportCityGMLToDB',body)
					
					//DEPLOY BINARIES - Deploy WFS - PRODUCTION
					echo 'DEPLOY BINARIES - Deploy WFS - PRODUCTION'
					body = "param1=" + commitid + "&param2=" + srs + "&param3=false"
					deployWFS = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/DeployWFS',body)
					
					//DEPLOY BINARIES - WFS Test - PRODUCTION
					echo 'DEPLOY BINARIES - WFS Test - PRODUCTION'
					body = "param1=" + commitid + "&param2=false"
					testWFS = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/WFSTest',body)
					
					//DEPLOY BINARIES - SaveCityGMLFinal - PRODUCTION
					echo 'DEPLOY BINARIES - SaveCityGMLFinal - PRODUCTION'
					body = cambiarCaracteresRaros(citygmlCorrectArea)
					body = "param1=" + body + "&param2=" + commitid
					saveCityGMLFinal = hacerhttprequest('http://3dcity-test.tecnalia.com/TecnaliaTesting/rest/Servicios/SaveCityGMLFinal',body)
					echo saveCityGMLFinal
				}
            }
        }
    }
}

def hacerhttprequest(laUrl,elBody) {
	def response = httpRequest consoleLogResponseBody: true, httpMode: 'POST', requestBody: elBody, url: laUrl, validResponseCodes: '100:399', passBuildParameters: false


	//println('Status: '+response.status)
	//println('Response: '+response.content)
	comprobarsiError(response.content)
	
	return response.content
}

def comprobarsiError(texto) {
    if(texto.contains('<ERROR>')) {
		currentBuild.result = 'FAILURE'
		throw err
    }
}

def comprobarsiErrorUAT(texto) {
    if(texto.contains('false')) {
		currentBuild.result = 'FAILURE'
		throw err
    }
}

def cambiarCaracteresRaros(citygml) {
	citygml = citygml.replaceAll("\\:", "%3A");
	citygml = citygml.replaceAll("\\/", "%2F");
	citygml = citygml.replaceAll(" ", "%20");
	citygml = citygml.replaceAll("\\|", "%7c");
	citygml = citygml.replaceAll("\\<", "%3C");
	citygml = citygml.replaceAll("\\?", "%3F");
	citygml = citygml.replaceAll("\\>", "%3E");
	citygml = citygml.replaceAll("\\=", "%3D");
	citygml = citygml.replaceAll("\\'", "%27");
	return citygml
}