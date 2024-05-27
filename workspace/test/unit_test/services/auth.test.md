import auth from '../../../src/services/auth'

export const authTest = () => {
  describe('test auth functions', () => {
    const id = '5042bb11-1722-4aba-9986-2ff948ac4f5c'
    let role = 'admin'
    let token = null

    const mockResponse = () => {
      const res = {}
      res.status = jest.fn().mockReturnValue(res)
      res.json = jest.fn().mockReturnValue(res)
      return res
    }
    const mockRequest = () => {
      return {
        rawHeaders: [
          'Authorization',
          'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjZhMDBjYWVjLTBhZDUtNDg3ZS1iNDBlLTMxYmQxMzE2ZjViYyIsImlhdCI6MTY3MTA5MzkzN30.LIWB-X0fWezWr6Zw0sLbt4SPDgQjr_vs6VExsRvNfWw',
        ],
        body: {
          id: jest.fn().mockReturnValue('5042bb11-1722-4aba-9986-2ff948ac4f5c'),
          role: 'user',
        },
      }
    }

    test('generate token', async () => {
      token = await auth.generateToken({ id, role })
      expect(typeof token).toBe('string')
    })

    test('verify token', async () => {
      const res = await auth.verifyToken(token)

      expect(typeof res).toBe('object')
      expect(res.id).toBe(id)
      expect(token).toEqual(
        expect.stringMatching(
          /^[A-Za-z0-9-_=]+\.[A-Za-z0-9-_=]+\.?[A-Za-z0-9-_.+/=]*$/
        )
      )
    })

    test('verifies token and check user\'s authorization', async () => {
      const req = mockRequest(),
        res = {},
        next = jest.fn()

      await auth.check(req, res, next)

      expect(typeof req).toBe('object')
      expect(next).toHaveBeenCalled()
    })

   
    test('user can only access to its own resources', () => {
      try {
        const req = {
          params: {
            id: '5042bb11-1722-4aba-9986-2ff948ac4f5c'
          },
          user: {
            role: 'user',
            id: '5042bb11-1722-4aba-9986-2ff948ac4f5c'
          }
        }
        const res = {}
        const next = jest.fn()

        auth.checkIdInParamsWithReqUserId(req, res, next)

        expect(next).toHaveBeenCalled()
      } catch (error) {
        console.log(error)
      }
    })

    test('denies admin from trying to submit any roleName but user', () => {
      try {
        const req = {
          body: {
            role: 'user'
          },
          user: {
            role: 'admin',
          }
        }
        const res = {}
        const next = jest.fn()

        auth.checkRequestRole(req, res, next)

        expect(next).toHaveBeenCalled()
      } catch (error) {
        console.log(error)
      }
    })
  })
}
