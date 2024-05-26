import utils from '../../../src/services/utils'

export const utilsTest = () => {
  describe('test utils functions', () => {
    test('creates a time', () => {
      const clause = '10m'
      const time = utils.afterTime(clause)

      expect(typeof time).toEqual('string')
    })

    test('generates token and sets user session', async () => {
      const key = 'key',
        seconds = 200,
        value = 'value'
      const redisToken = await utils.redisSetKey({ key, seconds, value })

      expect(typeof redisToken).toEqual('string')
      expect(redisToken).toBe('OK')
    })
  })
}
