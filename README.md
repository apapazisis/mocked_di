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
