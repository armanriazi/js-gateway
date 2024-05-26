import bcrypt from '../../../src/common/helper/bcrypt'

export const bcryptTest = () => {
  describe('test bcrypt functions', () => {
    const password = '1qaz!QAZ'
    let hashPass = null
    test('generate hashPass', async () => {
      hashPass = await bcrypt.createHashPass(password)
      expect(typeof hashPass).toBe('string')
    })
    test('compare hashPass', async () => {
      const res = await bcrypt.compareHashPass(password, hashPass)
      expect(typeof res).toBe('boolean')
      expect(res).toBe(true)
    })

    test('compare hashPass not correct', async () => {
      const wrongPass = '1qaz!qaz'

      const res = await bcrypt.compareHashPass(wrongPass, hashPass)
      expect(typeof res).toBe('boolean')
      expect(res).toBe(false)
    })
  })
}
