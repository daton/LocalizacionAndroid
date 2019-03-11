
# Geolocalizacion

## Implementamos la dependencia de geolocalización

```
  implementation 'com.google.android.gms:play-services-location:15.0.0'
```

## Implementar la siguiente interface


```
 ActivityCompat.OnRequestPermissionsResultCallback 
```
## Declarar las siguientes atributos en la **MainActivity**


```
 private val LOCATION_PERMISSION_REQUEST_CODE = 1
    private var mPermissionDenied = false
    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private lateinit var locationCallback: LocationCallback

    var miLati: Double? = null
    var miLongi: Double? = null

```

## Declaracion del fusedLocation

Despues del método ##setContentView##  declara lo siguiente

```
 fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)


        //La location callback
        locationCallback = object : LocationCallback() {
            override fun onLocationResult(locationResult: LocationResult?) {
                locationResult ?: return
                for (location in locationResult.locations) {
                    // Update UI with location data
                    // ...
                }
            }
          }
            
                enableMyLocation()
```

##Agregar el código del metodo **enableMiLocation()**

```
 private fun enableMyLocation() {
        if (ContextCompat.checkSelfPermission(
                this,
                Manifest.permission.ACCESS_FINE_LOCATION
            ) != PackageManager.PERMISSION_GRANTED
        ) {
            // Permission to access the location is missing.
            PermissionUtils.requestPermission(
                this, LOCATION_PERMISSION_REQUEST_CODE,
                Manifest.permission.ACCESS_FINE_LOCATION, true
            )
        }
    }
```

## Activar el método 

Activar los metodos de geolocalización y activación

```

override fun onResumeFragments() {
        super.onResumeFragments()
        if (mPermissionDenied) {
            // Permission was not granted, display error dialog.
            showMissingPermissionError()
            mPermissionDenied = false
        }
    }

    /**
     * Displays a dialog with error message explaining that the location permission is missing.
     */
    private fun showMissingPermissionError() {
        PermissionUtils.PermissionDeniedDialog.newInstance(true).show(supportFragmentManager, "dialog")
    }

    /***
     * ESTE METODO DE OBTENER UBICACION ES SUMAMEMTE IMPORTANTE, ES EL QUE NOS VA  Y SE INVOCA
     * EN LA CLASE INTERNA DE REGISTRARSE PARA QUE NOS DE EN AUTOMÁTICO LA GEOLOCALIZACION
     * CON EL THREAD QUE ESTA CONSTANTEMENTE ACTUALIZANDOSE
     */
    @SuppressLint("MissingPermission")
    fun obtenerUbicacion(momento: String) {


        // Este es otro cdigo

        val locationRequest = LocationRequest().apply {
            interval = 10000
            fastestInterval = 5000
            priority = LocationRequest.PRIORITY_BALANCED_POWER_ACCURACY
        }

        val builder = LocationSettingsRequest.Builder()
            .addLocationRequest(locationRequest)
        fusedLocationClient.requestLocationUpdates(locationRequest, locationCallback, null);


        fusedLocationClient.lastLocation
            .addOnSuccessListener { location: Location? ->
                println("PPPPEEEEERRRRRROOOO");

                Toast.makeText(
                    applicationContext,
                    "Loca " + location?.latitude + " Longi" + location?.longitude + " alti " + location?.altitude
                    ,
                    Toast.LENGTH_LONG
                ).show()

                miLati = location?.latitude
                miLongi = location?.longitude

            }

        println("ACEPTADO");

    }
```
