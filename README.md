Definition of Tests

#### What are unit tests?
Unit tests make sure that every unit of code functions correctly in isolation. The reasoning is that if every unit does its job correctly, then it will be easy to compose units together (like Legos), to create an application.

#### What are integration tests?
With integration tests, your goal is to simulate a real-world scenario in which many units of code collaborate to deliver some value. Another way to look at it is making sure all the units work together as a whole. That is because while each unit can function independently, it must also collaborate correctly with others.

#### Feature tests
With feature tests, you are testing the application by interacting with it just like a real user would do. So they are integration tests.
You click on links, buttons, fill in forms, interact with popups, etc.

Feature tests are all about what the user CAN see.

They give you the most bang for your buck in the sense that they make sure your users get value out of your application. Or from a different perspective, you are making sure the application is not broken in such a way that a user might notice.
Because feature tests need to mimic the user’s behavior as much and as close as possible they are using a web browser to do their work and that is very slow, which is their biggest downside.

### Simple Example Mocked DI Test

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
### Advanced Example Mocked DI Test

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
