
## dollar_volume.percentile_between(90,100)
it means take the top 10% items based on the dollar_volume

## mask, screen example
mask is used in filter.  
screen is used in pipeline.  
```
def make_pipeline():
    # Dollar volume factor
    dollar_volume = AverageDollarVolume(window_length=30)

    # High dollar volume filter
    high_dollar_volume = dollar_volume.percentile_between(90,100)

    # Top open securities filter (high dollar volume securities)
    top_open_price = USEquityPricing.open.latest.top(50, mask=high_dollar_volume)

    # Top percentile close price filter (high dollar volume, top 50 open price)
    high_close_price = USEquityPricing.close.latest.percentile_between(90, 100, mask=top_open_price)

    return Pipeline(
        screen=high_close_price
    )
``` 

## deciles
把已经排好序的样本依次分成10个数量相同的群体，我们就建立了一个叫decile的变量，它依次取10个值，1、2、3、4、5、6、7、8、9、10，diclie1包括违约概率值最高的10%的个体，diclie2包括下一个10%的群体，以此类推；
Is it similar to percentile_between()?