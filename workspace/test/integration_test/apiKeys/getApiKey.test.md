import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  adminPassword,
  password,
  mockExpireDate,
  mockMaxCount,
} from '../data/apikey.js'
import apiKeyService from '../requiredTest/apiKeyService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'

export const apiKeyGet = () => {
  describe('GET APIKEY ', () => {
    let apiKeyId
    let token
    let userToken
    let apiKey
    let userId
    let adminToken
    let adminId
    let superAdminId

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        const adminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'admin'
        }

        adminId = await usersService.create(adminInfo)

        delete adminInfo.role

        adminToken = await usersService.login(adminInfo)

        const userInfo = {
          username: randomUUID(),
          password: password,
          role: 'user'
        }

        userId = await usersService.create(userInfo)

        delete userInfo.role

        userToken = await usersService.login(userInfo)


        apiKey = {
          UserId: adminId,
          maxCount: mockMaxCount,
          expireDate: mockExpireDate,
        }

        apiKeyId = await apiKeyService.create(apiKey)

      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await apiKeyService.delete(apiKeyId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    it('apikey has been found', (done) => {
      try {
        request
          .get(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: apiKeyId,
                  }),
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can get apis', (done) => {
      try {
        request
          .get(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.arrayContaining([
                  expect.objectContaining({
                    id: apiKeyId,
                  }),
                ]),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not get a apikey', (done) => {
      try {
        request
          .get(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIKEY_ID} ${messages.m.NOT_FOUND}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apiKeyId is not found', (done) => {
      try {
        let wrongApiKeyId = 'kjh23j4h2jh34'

        request
          .get(`/api/apikeys/${wrongApiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token = 'kjh23j4h2jh34'

        request
          .get(`/api/apikeys/${apiKeyId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .get(`/api/apikeys/${apiKeyId}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
