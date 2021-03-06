
# Chain Strategy Annotation

Strategy extracts chain of strategies, passing each strategy execution result to the strategy. 

## Note: 

You don't have to mark source for each inner Strategy in chain list.
All ChainStrategy children should apply parent source.

```php
<?php

use VKMapperBundle\Annotation\MappingMeta\Strategy;
/**
    @Strategy\ChainStrategy(
        source="SourceObject", 
        list={
             @Strategy\GetterStrategy(method="getSum")
             @Strategy\ServiceClosureStrategy(
                 provider="ClosureServiceProvider",
                 method="multiply"
             )
         }
     )
 */
```

Example:
```php

<?php

use VKMapperBundle\Annotation\MappingMeta\DestinationClass;
use VKMapperBundle\Annotation\MappingMeta\Strategy;
use VKMapperBundle\Annotation\MappingMeta\SourceClass;

/**
 * Class ResultDto
 * @SourceClass
 */
class SourceObject
{
    /**
     * @Strategy\ChainStrategy(
     *     source="SourceObject",
     *     list={
     *          @Strategy\GetterStrategy(method="getSum"),
     *          @Strategy\ServiceClosureStrategy(
     *              provider="ClosureServiceProvider",
     *              method="multiply"
     *          ),
     *          @Strategy\ServiceClosureStrategy(
     *              provider="ClosureServiceProvider",
     *              method="divide"
     *          )
     * })
     */
    public $result;
    private $variableA = 10;
    private $variableB = 10;

    public function getSum(): int
    {
        return $this->variableA + $this->variableB;
    }
}

class ClosureServiceProvider
{
    private $multiplier = 10;
    private $divider = 2;

    public function multiply(): \Closure
    {
        return function (int $value):int {
            return $value * $this->multiplier; 
        };
    }
    
    public function divide(): \Closure
    {
        return function (int $value):int {
            return $value / $this->divider; 
        };
    }
}

/**
 * Class ResultDto
 * @DestinationClass
 */
class ResultDto
{
    public $result;
}

use DataMapper\MapperInterface;

/** @var MapperInterface $mapper */
$dto = $mapper->convert(new SourceObject(), ResultDto::class);
echo $dto->result; // 100

```

[back](..)