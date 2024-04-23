# MSFS Key Fob
This project enables interactive scenery elements with the use of a virtual Key Fob. If you are a scenery developer and you want your users to be able to interact with hangar doors, light systems or any other animation, this is the project for you!
On the flip side, if you are an aircraft developer and you want to provide a key fob in your aircraft, the code is simple and easy to integrate.

## XML Receiver (On Scenery SimObject)
The following code can be inserted in your scenery object. This object has to be brought into the scene as a SimObject for the animation to work. This code can then be attached to your animation as part of the model XML file in the ``<ModelInfo>`` section. Make sure to adapt the following parameters: 
- Change the latitude/longitude of the object
- Adjust the range as desired (default 0.1km)
- Change the channel so it is unique to each object. Replace all instances of ``ch1`` to ``ch2,3,4, etc.``)
```xml
<PartInfo>
	<Name>HangarDoor_Operation</Name>
	<AnimLength>800</AnimLength>
	<Animation>
		<Parameter>
			<Code>
				(Z:rf_ch1_lc, number) (L:rf_remote_ch1_time, number) &lt; if{
					(L:rf_remote_ch1_time, number) (Z:rf_ch1_tt, number) != if{
						(L:rf_remote_ch1_time, number) (&gt;Z:rf_ch1_tt, number)
						39.771427571 (L:rf_remote_ch1_lat, number) - abs (&gt;Z:rf_ch1_lat_dist, number)
						-119.896110241 (L:rf_remote_ch1_lon, number) - abs (&gt;Z:rf_ch1_lon_dist, number)
						(Z:rf_ch1_lat_dist, number) (Z:rf_ch1_lon_dist, number) + (&gt;Z:rf_ch1_dist, number)
						(Z:rf_ch1_dist, number) 111.32 * (&gt;Z:rf_ch1_dist, number)
						(Z:rf_ch1_dist, number) 0.1 &lt; if{
							(Z:door_ch1_state, number) 1 == if{
								0 (&gt;Z:door_ch1_state, number)
							} els{
								1 (&gt;Z:door_ch1_state, number)
							}
						}
					}
				}
				(E:SIMULATION TIME, seconds) (&gt;Z:rf_ch1_lc, number)
				(Z:door_ch1_state, number) 800 *
			</Code>
			<Lag>10</Lag>
		</Parameter>
	</Animation>
</PartInfo>
```

## JavaScript Emitter (on Avionics/JS)
If you are an aircraft developer or if you wish to create your own Key Fob, here is how you can send an RF signal to trigger an action from the JavaScript side.
- Change the channel so it is unique to each object. Replace all instances of ``ch1`` to ``ch2,3,4, etc.``)
```js
const time = SimVar.GetSimVarValue('E:SIMULATION TIME', 'Seconds');
const lat = SimVar.GetSimVarValue("PLANE LATITUDE", "Degrees");
const lon = SimVar.GetSimVarValue("PLANE LONGITUDE", "Degrees");
this.$api.variables.set('L:rf_remote_ch1_time', 'number', time + 1);
this.$api.variables.set('L:rf_remote_ch1_lat', 'number', lat);
this.$api.variables.set('L:rf_remote_ch1_lon', 'number', lon);
```

## XML Emitter (on Aircraft)
If you have a virtual Key Fob modeled in your aircraft and you want to use it without JavaScript, you can use this XML code when the button is clicked.
- Change the channel so it is unique to each object. Replace all instances of ``ch1`` to ``ch2,3,4, etc.``)
```rpn
(E:SIMULATION TIME, Seconds) 1 + (&gt;L:rf_remote_ch1_time, Number)
(A:PLANE LONGITUDE, Degrees) (&gt;L:rf_remote_ch1_lon, Number)
(A:PLANE LATITUDE, Degrees) (&gt;L:rf_remote_ch1_lat, Number)
```
