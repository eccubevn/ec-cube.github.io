### Sub Forward
- https://github.com/EC-CUBE/ec-cube/issues/2127
- Conventionally, when extending the processing of the controller without changing the main body, there were mainly the following methods.
    - Inherit and replace it with another instance
    - I will do my best at event handlers
    - By using `ApplicationTrait::forward($path, $requestParameters)`

```
/**
     * 購入画面表示
     *
     * @Route("/", name="shopping")
     * @Template("Shopping/index.twig")
     *
     * @param Application $app
     * @param Request $request
     * @return array
     */
    public function index(Application $app, Request $request)
    {
        // カートチェック
        $response = $app->forward($app->path("shopping/checkToCart"));
        if ($response->isRedirection() || $response->getContent()) {
            return $response;
        }

        // 受注情報を初期化
        $response = $app->forward($app->path("shopping/initializeOrder"));
        if ($response->isRedirection() || $response->getContent()) {
            return $response;
        }

        // 単価集計し, フォームを生成する
        $app->forwardChain($app->path("shopping/calculateOrder"))
            ->forwardChain($app->path("shopping/createForm"));

        // 受注のマイナスチェック
        $response = $app->forward($app->path("shopping/checkToMinusPrice"));
        if ($response->isRedirection() || $response->getContent()) {
            return $response;
        }

        // 複数配送の場合、エラーメッセージを一度だけ表示
        $app->forward($app->path("shopping/handleMultipleErrors"));

        $Order = $app['request_scope']->get('Order');
        $form = $app['request_scope']->get(OrderType::class);

        return [
            'form' => $form->createView(),
            'Order' => $Order
        ];
    }
```

- Note: 
    - It is also possible to connect multiple forwards in succession by using `forwardChain`.
    - The twig template can call controller as sub request by `render`.