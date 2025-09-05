```Java
    /**
     * 接收OneNet物联网平台 5D-8G设备数据推送
     */
    @GetMapping("/gps")
    public String gpsListener(HttpServletRequest body) {
        String token = "test";
        OneNetUtil.BodyObj obj = OneNetUtil.resolveBody(body, false);
        if (obj != null) {
            boolean dataRight = OneNetUtil.checkSignature(obj, token);
            if (dataRight) {
                return (String) obj.getMsg();
            } else {
                return "error";
            }
        } else {
            return "data receive: body empty error";
        }
    }
    /**
     * 接收OneNet物联网平台 5D-8G设备数据推送
     */
    @PostMapping("/gps")//HttpServletRequest body,String s
    public CommonResult<BlankVo> gpsListener(@RequestBody PositionMsg positionMsg) {
//        Position position = positionMsg.getData();
//        GeoFenceUtils.isPointInGeoFence(, position.getLon() + "," + position.getLat());
//        return map.toString();
        return deviceService.gpsListener(positionMsg);
    }
    @GetMapping("/gps")
    public String dataListener(HttpServletRequest body) {
        String token = "test";
        OneNetUtil.BodyObj obj = OneNetUtil.resolveBody(body, false);
        if (obj != null) {
            boolean dataRight = OneNetUtil.checkSignature(obj, token);
            if (dataRight) {
                return (String) obj.getMsg();
            } else {
                return "error";
            }
        } else {
            return "data receive: body empty error";
        }
    }
```