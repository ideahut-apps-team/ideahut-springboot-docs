# SysParam
Menyimpan konfigurasi aplikasi ke database dan redis.

## Bean
``` java
@Bean
protected SysParamHandler sysParamHandler(
    DataMapper dataMapper,
    RedisTemplate<String, byte[]> redisTemplate,
    EntityTrxManager entityTrxManager
) {
    return new SysParamHandlerImpl()
    .setDataMapper(dataMapper)
    .setRedisTemplate(redisTemplate)
    .setReloader(new SysParamReloader() {
        @Override
        public List<SysParamDto> reload(Collection<String> sysCodes) {
            TrxManagerInfo trxManagerInfo = entityTrxManager.getDefaultTrxManagerInfo();
            return trxManagerInfo.transaction(new SessionCallable<List<SysParamDto>>() {
                @Override
                public List<SysParamDto> call(Session session) throws Exception {
                    boolean notEmpty = sysCodes != null && !sysCodes.isEmpty();
                    Query<SysParam> query = session.createQuery(
                        "from SysParam " + (notEmpty ? "where id.sysCode in (?1)": ""), 
                        SysParam.class
                    );
                    if (notEmpty) {
                        query.setParameter(1, sysCodes);
                    }
                    List<SysParamDto> dtos = new ArrayList<SysParamDto>();
                    List<SysParam> entities = query.getResultList();
                    while (!entities.isEmpty()) {
                        SysParam entity = entities.remove(0);
                        SysParamDto dto = new SysParamDto(entity.getId().getSysCode(), entity.getId().getParamCode());
                        BeanUtils.copyProperties(entity, dto, "id");
                        dtos.add(dto);
                    }
                    return dtos;
                }
            });
        }
    });
}
```