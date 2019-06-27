### Simple Example

```
namespace SitePoint\Weather;

interface WeatherServiceInterface
{
    /**
     * Return the Celsius temperature
     *
     * @return float
     */
    public function getTempCelsius();
}
```

```
namespace SitePoint\Weather;

class TemperatureService
{
   private $weatherService;

   public function __construct(WeatherServiceInterface $weatherService) {
       $this->weatherService = $weatherService;
   }

   public function getTempFahrenheit() {
       return ($this->weatherService->getTempCelsius() * 1.8000) + 32;
   }
}
```

```
class TemperatureServiceTest extends \PHPUnit_Framework_TestCase
{

   public function testGetTempFahrenheit() {
       $weatherServiceMock = \Mockery::mock('SitePoint\Weather\WeatherServiceInterface');
       $weatherServiceMock->shouldReceive('getTempCelsius')->once()->andReturn(25);

       $temperatureService = new TemperatureService($weatherServiceMock);
       $this->assertEquals(77, $temperatureService->getTempFahrenheit());
   }
}
```
### Advanced Example

We add the following method to our current WeatherServiceInterface.
```
/**
 * Return the Celsius temperature by hour
 * 
 * @param $hour
 * @return float
 */
public function getTempByHour($hour);
```

We create a new method in our TemperatureService which calculates the average temperature.
```
/**
 * Get average temperature of the night
 * 
 * @return float
 */
public function getAvgNightTemp() {
    $nightHours = array(0, 1, 2, 3, 4, 5, 6);
    $totalTemperature = 0;

    foreach($nightHours as $hour) {
        $totalTemperature += $this->weatherService->getTempByHour($hour);
    }

    return $totalTemperature / count($nightHours);
}
```

Let’s have a look at our test method.
```
public function testGetAvgNightTemp() {
    $weatherServiceMock = \Mockery::mock('SitePoint\Weather\WeatherServiceInterface');
    $weatherServiceMock->shouldReceive('getTempByHour')
        ->times(7)
        ->with(\Mockery::anyOf(0, 1, 2, 3, 4, 5, 6))
        ->andReturn(14, 13, 12, 11, 12, 12, 13);

    $temperatureService = new TemperatureService($weatherServiceMock);
    $this->assertEquals(12.43, $temperatureService->getAvgNightTemp());
}
```
