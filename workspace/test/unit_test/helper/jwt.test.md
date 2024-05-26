import jwt from '../../../src/common/helper/jwt'

export const jwtTest = () => {
  describe('test jwt functions', () => {
    const id = '36b2520d-752e-4f01-9bed-76d863b9fb2b',
      Role = 'admin'
    let token = null

    test('creates a token for logged in user', async () => {
      token = await jwt.createToken({ id, Role })

      expect(typeof token).toBe('string')
    })

    test('verifies if received token is valid', async () => {
      const valid = await jwt.verifyToken(token)

      expect(typeof valid).toBe('object')
      expect(valid.id).toEqual('36b2520d-752e-4f01-9bed-76d863b9fb2b')
      expect(valid.Role).toEqual('admin')
    })

    test('verifies if has been called with token', async () => {

      const somethingSpy = jest.spyOn(jwt, 'verifyToken')
      somethingSpy(token)

      expect(somethingSpy).toHaveBeenCalledWith(token);
    })

    test('token is invalid', async () => {
      const valid = await jwt.verifyToken(token)

      expect(typeof valid).toBe('object')
      expect(valid.id).not.toEqual('59t7923p-853q-4f01-0acr-00c010b9fb2b')
      expect(valid.Role).not.toEqual('user')
    })
  })
}
